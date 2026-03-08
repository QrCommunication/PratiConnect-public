# Webhooks Documentation - PratiConnect

This document describes the webhook endpoints implementation for payment providers (Stripe and SumUp).

## Table of Contents

- [Overview](#overview)
- [Security Requirements](#security-requirements)
- [Stripe Webhooks](#stripe-webhooks)
- [SumUp Webhooks](#sumup-webhooks)
- [Idempotency](#idempotency)
- [Error Handling](#error-handling)
- [Testing](#testing)
- [Monitoring](#monitoring)

---

## Overview

PratiConnect implements webhook endpoints for external payment providers to receive real-time event notifications:

- **Stripe**: Platform subscriptions (admin → practitioner) AND session payments (practitioner → patient)
- **SumUp**: Session payments only (practitioner → patient)

### Critical Requirements

✅ **NO Authentication Middleware**: Webhooks bypass normal authentication
✅ **Signature Verification**: MANDATORY for all webhooks
✅ **Idempotent Processing**: Duplicate events are detected and ignored
✅ **Return 200 OK**: Even on errors to prevent infinite retries
✅ **Never Modify Request Body**: Breaks signature verification
✅ **Quick Response (<200ms)**: Acknowledge first, process after

---

## Security Requirements

### 1. Signature Verification

All webhooks MUST verify signatures before processing:

**Stripe**: Uses Stripe SDK's `Webhook::constructEvent()`
```php
$event = Webhook::constructEvent(
    $payload,           // Raw request body
    $signature,         // Stripe-Signature header
    $webhookSecret      // From admin_settings
);
```

**SumUp**: Uses HMAC-SHA256
```php
$expectedSignature = hash_hmac('sha256', $payload, $webhookSecret);
if (!hash_equals($expectedSignature, $signature)) {
    throw new \InvalidArgumentException('Invalid signature');
}
```

### 2. Webhook Secrets Storage

**Stripe Global** (Platform subscriptions):
- Stored in `admin_settings` table
- Key: `stripe_config`
- Field: `webhook_secret`

**Stripe Tenant** (Session payments):
- Stored in `tenants.settings` JSONB
- Path: `stripe_config.webhook_secret`

**SumUp Tenant**:
- Stored in `tenants.settings` JSONB
- Path: `sumup_config.webhook_secret`

### 3. Request Flow

```
1. Webhook arrives → No auth middleware
2. Extract raw body (BEFORE any processing)
3. Extract signature header
4. Verify signature → Fail = 400 Bad Request
5. Check idempotency → Duplicate = 200 OK
6. Log event to webhook_events table
7. Process event
8. Mark as processed
9. Return 200 OK (even if processing failed)
```

---

## Stripe Webhooks

### Endpoint

```
POST /webhooks/stripe
```

### Events Handled

#### Session Payments

| Event | Action |
|-------|--------|
| `payment_intent.succeeded` | Mark session as paid, update patient total_spent |
| `payment_intent.payment_failed` | Log failure, create audit log |
| `checkout.session.completed` | Process checkout payment |

#### Platform Subscriptions

| Event | Action |
|-------|--------|
| `customer.subscription.updated` | Update tenant subscription_status |
| `customer.subscription.deleted` | Mark tenant as expired |
| `invoice.paid` | Restore active status if past_due |
| `invoice.payment_failed` | Mark tenant as past_due |

### Configuration Required

**Admin Panel** (`/admin/settings/stripe`):
```json
{
  "publishable_key": "pk_live_...",
  "secret_key": "sk_live_...",
  "webhook_secret": "whsec_..."
}
```

**Stripe Dashboard** (https://dashboard.stripe.com/webhooks):
1. Add endpoint: `https://praticonnect.com/api/v1/webhooks/stripe`
2. Select events:
   - `payment_intent.succeeded`
   - `payment_intent.payment_failed`
   - `checkout.session.completed`
   - `customer.subscription.updated`
   - `customer.subscription.deleted`
   - `invoice.paid`
   - `invoice.payment_failed`
3. Copy webhook signing secret → Admin panel

### Request Headers

```
Content-Type: application/json
Stripe-Signature: t=1234567890,v1=abc123...
```

### Example Payload (payment_intent.succeeded)

```json
{
  "id": "evt_1234567890",
  "type": "payment_intent.succeeded",
  "data": {
    "object": {
      "id": "pi_1234567890",
      "amount": 6000,
      "currency": "eur",
      "status": "succeeded",
      "metadata": {
        "session_id": "uuid-of-session",
        "patient_id": "uuid-of-patient",
        "tenant_id": "uuid-of-tenant"
      }
    }
  }
}
```

### Processing Logic

```php
// 1. Find session by ID in metadata
$sessionId = $paymentIntent->metadata->session_id;
$session = Session::find($sessionId);

// 2. Check if already paid (idempotency)
if ($session->payment_status === Session::PAYMENT_PAID) {
    return; // Already processed
}

// 3. Update session in transaction
DB::transaction(function () use ($session, $paymentIntent) {
    $session->update([
        'payment_status' => Session::PAYMENT_PAID,
        'payment_collected' => true,
    ]);

    $session->patient->increment('total_spent', $session->amount);

    AuditLog::create([...]);
});
```

---

## SumUp Webhooks

### Endpoint

```
POST /webhooks/sumup
```

### Events Handled

| Event | Action |
|-------|--------|
| `CHECKOUT_COMPLETED` | Mark session as paid, update patient total_spent |
| `CHECKOUT_FAILED` | Log failure, create audit log |
| `REFUND_COMPLETED` | Update session status (refunded/partial) |

### Configuration Required

**Tenant Settings** (`/settings/integrations/sumup`):
```json
{
  "client_id": "com.sumup.praticonnect",
  "client_secret": "...",
  "webhook_secret": "..."
}
```

**SumUp Developer Portal** (https://developer.sumup.com):
1. Create webhook
2. URL: `https://praticonnect.com/api/v1/webhooks/sumup`
3. Events: `CHECKOUT_COMPLETED`, `CHECKOUT_FAILED`, `REFUND_COMPLETED`
4. Copy webhook secret → Tenant settings

### Request Headers

```
Content-Type: application/json
X-SumUp-Signature: abc123...
```

### Example Payload (CHECKOUT_COMPLETED)

```json
{
  "event_id": "evt_abc123",
  "event_type": "CHECKOUT_COMPLETED",
  "data": {
    "checkout_id": "co_abc123",
    "amount": 6000,
    "currency": "EUR",
    "status": "PAID",
    "metadata": {
      "session_id": "uuid-of-session",
      "tenant_id": "uuid-of-tenant"
    }
  }
}
```

### Processing Logic

```php
// 1. Find session by ID in metadata
$sessionId = $event['data']['metadata']['session_id'];
$session = Session::find($sessionId);

// 2. Check if already paid (idempotency)
if ($session->payment_status === Session::PAYMENT_PAID) {
    return;
}

// 3. Update session
DB::transaction(function () use ($session, $checkoutId) {
    $session->update([
        'payment_status' => Session::PAYMENT_PAID,
        'payment_collected' => true,
        'payment_method' => Session::METHOD_SUMUP,
        'sumup_checkout_id' => $checkoutId,
    ]);

    $session->patient->increment('total_spent', $session->amount);

    AuditLog::create([...]);
});
```

---

## Idempotency

### Database Table: `webhook_events`

```sql
CREATE TABLE webhook_events (
    id BIGINT PRIMARY KEY,
    provider VARCHAR(50),           -- 'stripe' or 'sumup'
    event_id VARCHAR(255) UNIQUE,   -- Provider's event ID
    event_type VARCHAR(255),        -- Event type
    payload TEXT,                   -- JSON payload
    processed BOOLEAN DEFAULT FALSE,
    processed_at TIMESTAMP,
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);
```

### Duplicate Detection

Before processing any webhook:

```php
if (DB::table('webhook_events')
    ->where('provider', 'stripe')
    ->where('event_id', $event->id)
    ->where('processed', true)
    ->exists()) {

    return response()->json(['status' => 'ok', 'message' => 'duplicate']);
}
```

### Session-Level Idempotency

Additional check at session level:

```php
if ($session->payment_status === Session::PAYMENT_PAID) {
    Log::info('Session already marked as paid');
    return; // Don't process again
}
```

### Why Two Levels?

1. **Webhook level**: Prevents duplicate processing of the same event
2. **Session level**: Prevents race conditions if multiple events modify the same session

---

## Error Handling

### Return Status Codes

| Scenario | Status Code | Behavior |
|----------|-------------|----------|
| Invalid signature | 400 | Don't retry |
| Missing signature | 400 | Don't retry |
| Processing error | 200 | Prevent retry |
| Success | 200 | OK |
| Duplicate event | 200 | OK (idempotent) |

### Why Return 200 on Processing Errors?

Stripe and SumUp retry failed webhooks with exponential backoff. If we return 500 for a processing error (e.g., session not found), the webhook will retry forever.

**Instead**:
- Return 200 OK to acknowledge receipt
- Log error for manual investigation
- Fix the issue manually if needed

```php
try {
    $this->processEvent($event);
    return response()->json(['status' => 'ok']);
} catch (\Exception $e) {
    Log::error('Webhook processing failed', [
        'error' => $e->getMessage(),
        'event_id' => $event->id,
    ]);

    // Return 200 to prevent retries
    return response()->json(['status' => 'error'], 200);
}
```

### Logging

All webhook events are logged to:
1. **Database** (`webhook_events` table)
2. **Laravel logs** (`storage/logs/laravel.log`)
3. **Audit logs** (`audit_logs` table) for payment changes

---

## Testing

### Local Testing with ngrok

1. Start ngrok:
```bash
ngrok http 8000
```

2. Configure webhook URL in Stripe/SumUp dashboard:
```
https://abc123.ngrok.io/webhooks/stripe
https://abc123.ngrok.io/webhooks/sumup
```

3. Trigger test events from provider dashboard

### Stripe CLI Testing

```bash
# Install Stripe CLI
brew install stripe/stripe-cli/stripe

# Login
stripe login

# Forward webhooks to local server
stripe listen --forward-to localhost:8000/webhooks/stripe

# Trigger test events
stripe trigger payment_intent.succeeded
stripe trigger payment_intent.payment_failed
stripe trigger customer.subscription.updated
```

### Manual Testing

Use cURL to send test webhooks:

```bash
curl -X POST http://localhost:8000/webhooks/stripe \
  -H "Content-Type: application/json" \
  -H "Stripe-Signature: test-signature" \
  -d '{
    "id": "evt_test_123",
    "type": "payment_intent.succeeded",
    "data": {
      "object": {
        "id": "pi_test_123",
        "metadata": {
          "session_id": "your-test-session-uuid"
        }
      }
    }
  }'
```

**Note**: This will fail signature verification unless you use a valid signature.

### Test Checklist

- [ ] Valid webhook is processed correctly
- [ ] Invalid signature returns 400
- [ ] Duplicate event is ignored (idempotency)
- [ ] Processing error returns 200 (no retry)
- [ ] Session is marked as paid
- [ ] Patient total_spent is updated
- [ ] Audit log is created
- [ ] Webhook is logged to database

---

## Monitoring

### Metrics to Track

1. **Webhook success rate**: % of successfully processed webhooks
2. **Processing time**: Time to process webhook (target: <100ms)
3. **Duplicate rate**: % of duplicate webhooks received
4. **Error rate**: % of webhooks with processing errors

### Alerts

Set up alerts for:
- High error rate (>5%)
- Processing time >500ms
- Signature verification failures

### Database Queries

**Check recent webhooks**:
```sql
SELECT provider, event_type, processed, created_at
FROM webhook_events
ORDER BY created_at DESC
LIMIT 100;
```

**Check unprocessed webhooks**:
```sql
SELECT provider, event_id, event_type, created_at
FROM webhook_events
WHERE processed = false
AND created_at > NOW() - INTERVAL '1 hour';
```

**Check duplicate rate**:
```sql
SELECT provider, COUNT(*) as total,
       COUNT(DISTINCT event_id) as unique_events
FROM webhook_events
WHERE created_at > NOW() - INTERVAL '24 hours'
GROUP BY provider;
```

---

## Troubleshooting

### Webhook Not Received

1. Check provider dashboard for delivery failures
2. Verify webhook URL is correct
3. Check firewall/security rules
4. Test with ngrok locally

### Signature Verification Failed

1. Verify webhook secret is correct
2. Check that raw body is not modified before verification
3. Ensure content-type is application/json
4. Check for middleware that modifies request

### Session Not Updated

1. Check `webhook_events` table to see if event was received
2. Check Laravel logs for errors
3. Verify session_id in metadata is correct
4. Check if session was already marked as paid (idempotency)

### Infinite Retries

If webhooks are being retried infinitely:
1. Check that endpoint returns 200 OK
2. Verify processing doesn't throw unhandled exceptions
3. Check provider dashboard for retry settings

---

## Best Practices

✅ **Always verify signatures** - Never process unverified webhooks
✅ **Return 200 quickly** - Acknowledge receipt in <200ms
✅ **Use idempotency** - Handle duplicate events gracefully
✅ **Log everything** - Webhooks are hard to debug without logs
✅ **Never trust payload alone** - Always re-fetch from provider API for critical operations
✅ **Handle missing data** - Metadata might be missing or malformed
✅ **Use transactions** - Ensure database consistency
✅ **Monitor closely** - Webhook failures can cause revenue loss

---

## References

- [Stripe Webhooks Documentation](https://stripe.com/docs/webhooks)
- [SumUp Webhooks Documentation](https://developer.sumup.com/docs/webhooks)
- [OWASP: Webhook Security](https://cheatsheetseries.owasp.org/cheatsheets/Webhook_Security_Cheat_Sheet.html)
- PratiConnect CLAUDE.md: Section 9.2 (Security & Compliance)

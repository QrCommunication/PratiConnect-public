# Calendar Services - PratiConnect

This document describes the calendar services that have been created for PratiConnect.

## Files Created

### Services

1. **GoogleCalendarService.php** (`/home/rony/Projets/PratiConnect/app/Services/Calendar/GoogleCalendarService.php`)
   - `connect()` - OAuth2 connection flow with Google Calendar
   - `disconnect()` - Revoke Google Calendar access
   - `listEvents()` - List events from Google Calendar
   - `createEvent()` - Create event for appointment
   - `updateEvent()` - Update existing Google Calendar event
   - `deleteEvent()` - Delete event from Google Calendar
   - `syncFromGoogle()` - Import events from Google Calendar
   - `syncToGoogle()` - Push appointments to Google Calendar

2. **AvailabilityService.php** (`/home/rony/Projets/PratiConnect/app/Services/Calendar/AvailabilityService.php`)
   - `getAvailableSlots()` - Get available time slots for booking
   - `isSlotAvailable()` - Check if specific slot is available
   - `calculateNextAvailable()` - Find next available slot
   - `isInClosurePeriod()` - Check if date is in closure period
   - `applyClosurePeriods()` - Filter out closure periods
   - `applyAvailabilityRules()` - Apply weekly availability patterns
   - `getAvailableDates()` - Get all available dates in period
   - `validateBooking()` - Validate if booking can be made

3. **AppointmentService.php** (`/home/rony/Projets/PratiConnect/app/Services/Calendar/AppointmentService.php`)
   - `book()` - Book a new appointment
   - `confirm()` - Confirm pending appointment
   - `cancel()` - Cancel with cancellation notice check
   - `reschedule()` - Reschedule to new time
   - `scheduleReminders()` - Schedule email/SMS reminders
   - `getUpcoming()` - Get upcoming appointments
   - `getForDate()` - Get appointments for specific date
   - `getForRange()` - Get appointments for date range
   - `markAsNoShow()` - Mark as no-show
   - `start()` - Start appointment (in-progress)
   - `complete()` - Complete appointment
   - `getStatistics()` - Get appointment statistics

### Controller

**CalendarController.php** (`/home/rony/Projets/PratiConnect/app/Http/Controllers/Api/V1/CalendarController.php`)

All endpoints include complete OpenAPI documentation:

- `GET /api/v1/practitioners/{id}/availability` - Public endpoint for booking widget
- `GET /api/v1/appointments` - List appointments (practitioner)
- `POST /api/v1/appointments` - Book appointment
- `PUT /api/v1/appointments/{id}/confirm` - Confirm appointment
- `DELETE /api/v1/appointments/{id}` - Cancel appointment
- `PUT /api/v1/appointments/{id}/reschedule` - Reschedule appointment
- `GET /api/v1/appointments/statistics` - Get statistics
- `GET /api/v1/calendar/google/connect` - Get Google OAuth URL
- `POST /api/v1/calendar/google/callback` - Handle OAuth callback
- `DELETE /api/v1/calendar/google` - Disconnect Google Calendar

### Models

1. **CalendarSettings.php** - Calendar configuration for practitioners
   - Session duration, buffer time
   - Booking window, minimum notice
   - Cancellation policy
   - Google Calendar integration settings

2. **Appointment.php** - Already existed, ready to use
3. **AvailabilityRule.php** - Already existed, ready to use
4. **ClosurePeriod.php** - Already existed, ready to use

### Jobs

**SendAppointmentReminder.php** (`/home/rony/Projets/PratiConnect/app/Jobs/SendAppointmentReminder.php`)
- Handles both email and SMS reminders
- Checks appointment validity
- Respects SMS option activation
- Automatic retry on failure

### Notifications

1. **AppointmentReminder.php** - 24h reminder notification
2. **AppointmentConfirmed.php** - Booking confirmation
3. **AppointmentCancelled.php** - Cancellation notification
4. **AppointmentRescheduled.php** - Rescheduling notification

All notifications are multilingual (FR/EN/HE).

## Dependencies Added

Updated `composer.json` to include:
- `google/apiclient` ^2.16 - Google Calendar API client

## Configuration

Added to `config/services.php`:
```php
'google' => [
    'client_id' => env('GOOGLE_CLIENT_ID'),
    'client_secret' => env('GOOGLE_CLIENT_SECRET'),
    'redirect_uri' => env('GOOGLE_REDIRECT_URI', env('APP_URL') . '/api/v1/calendar/google/callback'),
],
```

## Environment Variables Required

Add to `.env`:
```
# Google Calendar
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GOOGLE_REDIRECT_URI=https://praticonnect.com/api/v1/calendar/google/callback
```

## Routes Added

All routes added to `routes/api.php`:
- Calendar and appointments routes under `auth:sanctum` middleware
- Public availability endpoint (no auth)
- Google Calendar integration routes

## Installation Steps

1. Install Google API client:
```bash
composer install
```

2. Add Google OAuth credentials:
   - Go to Google Cloud Console
   - Create OAuth 2.0 credentials
   - Add to .env

3. Run migrations (if calendar tables don't exist yet):
```bash
php artisan migrate
```

4. Clear cache:
```bash
php artisan config:clear
php artisan route:clear
```

## Usage Examples

### Book an Appointment

```bash
curl -X POST https://praticonnect.com/api/v1/appointments \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "practitioner_id": "uuid",
    "patient_id": "uuid",
    "starts_at": "2024-12-25T10:00:00+01:00",
    "duration_minutes": 60,
    "appointment_type": "session"
  }'
```

### Get Available Slots (Public)

```bash
curl -X GET "https://praticonnect.com/api/v1/practitioners/{id}/availability?date=2024-12-25"
```

### Connect Google Calendar

```bash
# 1. Get auth URL
curl -X GET https://praticonnect.com/api/v1/calendar/google/connect \
  -H "Authorization: Bearer {token}"

# 2. Redirect user to returned auth_url
# 3. Handle callback with code
curl -X POST https://praticonnect.com/api/v1/calendar/google/callback \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{"code": "oauth_code_from_google"}'
```

## Features Implemented

### Core Features
- ✅ Appointment booking with validation
- ✅ Availability slot calculation
- ✅ Weekly availability patterns (AvailabilityRule)
- ✅ Closure periods (holidays, vacations)
- ✅ Booking window and minimum notice enforcement
- ✅ Cancellation policy with notice period
- ✅ Appointment confirmation/cancellation/rescheduling
- ✅ Appointment statistics

### Google Calendar Integration
- ✅ OAuth2 connection flow
- ✅ Automatic event creation
- ✅ Event updates on reschedule
- ✅ Event deletion on cancel
- ✅ Two-way sync (import from Google, export to Google)
- ✅ Encrypted refresh token storage

### Notifications
- ✅ Email reminders (24h before)
- ✅ SMS reminders (if option enabled)
- ✅ Booking confirmation emails
- ✅ Cancellation notifications
- ✅ Rescheduling notifications
- ✅ Multilingual support (FR/EN/HE)

### API Documentation
- ✅ Complete OpenAPI annotations
- ✅ Request/response examples
- ✅ Validation rules documented
- ✅ Error responses documented

## Architecture Highlights

### Multi-tenant Isolation
All services respect tenant isolation via TenantScope on models.

### Validation Layers
- Service-level validation (business rules)
- Controller-level validation (request validation)
- Model-level validation (database constraints)

### Security
- Encrypted Google refresh tokens (Laravel Crypt)
- Authorization checks (practitioner owns appointment)
- Rate limiting ready
- CSRF protection

### Performance
- Efficient slot calculation algorithm
- Minimal database queries
- Eager loading of relationships
- Query result caching ready

### Testing Ready
- Dependency injection throughout
- Service pattern for easy mocking
- Clear separation of concerns

## Next Steps

1. Create database migrations for calendar tables (if not exist)
2. Set up Google OAuth credentials
3. Configure reminder scheduler (cron job)
4. Test appointment flow end-to-end
5. Add frontend integration
6. Set up monitoring for reminder jobs

## Notes

- SMS reminders only work if tenant has `sms_option_active` enabled
- Google Calendar sync is optional per practitioner
- All times use practitioner's timezone (from tenant)
- Reminders are scheduled as delayed jobs (Laravel queue)
- Public availability endpoint allows booking widget on external sites

## Support

For issues or questions:
- Check logs: `storage/logs/laravel.log`
- Queue monitoring: Laravel Horizon
- API documentation: `/api/documentation` (Swagger)

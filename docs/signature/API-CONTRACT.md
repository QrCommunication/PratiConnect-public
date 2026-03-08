# API Contract - Signature Electronique

**Version:** 1.0.0
**Date:** 2026-01-09
**Auteur:** Project Architect

---

## 1. Vue d'ensemble

### 1.1 Types de signature

| Type | Provider | Valeur légale | Coût | Logging |
|------|----------|---------------|------|---------|
| `simple` | Local (certificat serveur) | Probante (non-qualifiée) | Gratuit | Hash chain immuable |
| `eidas` | OpenAPI EU-SES | eIDAS qualifiée | Crédits | Hash chain + audit OpenAPI |

### 1.2 Flux de signature

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         CRÉATION DEMANDE                                     │
│  Praticien sélectionne: Document + Type (simple/eIDAS) + Signataires        │
└─────────────────────────────┬───────────────────────────────────────────────┘
                              │
              ┌───────────────┴───────────────┐
              │                               │
              ▼                               ▼
┌─────────────────────────┐     ┌─────────────────────────────────────────────┐
│     SIMPLE (Local)       │     │              eIDAS (OpenAPI)                 │
│                          │     │                                              │
│ 1. Afficher pad          │     │ 1. Transmettre à OpenAPI avec:               │
│ 2. Case consentement     │     │    - Document base64                         │
│ 3. Signature manuscrite  │     │    - Signataires                             │
│ 4. Certificat serveur    │     │    - Thème UI (couleurs, logo)               │
│ 5. Hash chain log        │     │ 2. Patient reçoit lien + OTP                 │
│                          │     │ 3. Signe via interface OpenAPI               │
│                          │     │ 4. Webhook retourne résultat                 │
└─────────────────────────┘     └─────────────────────────────────────────────┘
              │                               │
              └───────────────┬───────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           LOGGING PROBANT                                    │
│  - Hash chain immuable (comme RecordingConsentLog)                          │
│  - IP + User-Agent + Timestamp                                               │
│  - Nom patient + Praticien + Acte                                           │
│  - Hash SHA-256 document                                                     │
│  - Metadata signataire (si eIDAS: cert chain, TSA)                          │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Endpoints API

### 2.1 Créer une demande de signature

**POST** `/api/v1/signature-requests`

#### Request Body

```typescript
interface CreateSignatureRequest {
  document_id: string;                    // UUID du document à signer
  signature_type: 'simple' | 'eidas';     // Type de signature
  signers: SignerConfig[];                // Liste des signataires
  consent_text?: string;                  // Texte personnalisé (défaut: texte légal)
  expires_in_days?: number;               // Expiration (défaut: 7)
  ui_customization?: UiCustomization;     // Thème pour eIDAS
  session_id?: string;                    // Lier à une session (pour logging acte)
}

interface SignerConfig {
  type: 'patient' | 'practitioner';       // Type de signataire
  name?: string;                          // Nom (auto si patient lié)
  email?: string;                         // Email (requis pour eIDAS)
  mobile?: string;                        // Mobile pour OTP SMS (eIDAS)
  authentication?: ('email' | 'sms')[];   // Méthode OTP (eIDAS)
}

interface UiCustomization {
  // Couleurs
  primary_color: string;                  // #hex (boutons, accents)
  secondary_color: string;                // #hex (fond sidebar)
  header_color: string;                   // #hex (fond header)
  text_color: string;                     // #hex (texte principal)

  // Logo
  logo_url?: string;                      // URL du logo praticien

  // Redirections
  complete_url?: string;                  // URL après signature
  cancel_url?: string;                    // URL si annulation

  // Options d'affichage
  hide_sidebar?: boolean;
  hide_header?: boolean;
}
```

#### Response (201 Created)

```typescript
interface SignatureRequestResponse {
  data: {
    id: string;
    document_id: string;
    patient_id: string;
    signature_type: 'simple' | 'eidas';
    status: 'pending' | 'partially_signed' | 'completed' | 'expired' | 'cancelled';
    signers: Signer[];
    document_hash: string;                // SHA-256 du contenu
    consent_text: string;
    expires_at: string;                   // ISO-8601
    created_at: string;

    // Pour eIDAS uniquement
    signing_urls?: Record<string, string>; // {email: url}

    // Pour simple uniquement
    signing_token?: string;               // Token pour page de signature
  };
  message: string;
}
```

#### PHP Types

```php
// App\Http\Requests\Signature\CreateSignatureRequest
class CreateSignatureRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'document_id' => ['required', 'uuid', 'exists:documents,id'],
            'signature_type' => ['required', 'in:simple,eidas'],
            'signers' => ['required', 'array', 'min:1'],
            'signers.*.type' => ['required', 'in:patient,practitioner'],
            'signers.*.email' => ['required_if:signature_type,eidas', 'email'],
            'signers.*.mobile' => ['nullable', 'string'],
            'signers.*.authentication' => ['nullable', 'array'],
            'consent_text' => ['nullable', 'string', 'max:2000'],
            'expires_in_days' => ['nullable', 'integer', 'min:1', 'max:30'],
            'ui_customization' => ['nullable', 'array'],
            'ui_customization.primary_color' => ['nullable', 'regex:/^#[0-9A-Fa-f]{6}$/'],
            'ui_customization.logo_url' => ['nullable', 'url'],
            'session_id' => ['nullable', 'uuid', 'exists:sessions,id'],
        ];
    }
}
```

---

### 2.2 Signer avec pad simple

**POST** `/api/v1/signature-requests/{id}/sign-simple`

#### Request Body

```typescript
interface SignSimpleRequest {
  signature_image: string;      // Base64 PNG de la signature
  consent_confirmed: boolean;   // Case cochée = true (requis)
  signer_type: 'patient' | 'practitioner';
}
```

#### Validation serveur (automatique)

Le serveur capture automatiquement:
- IP address
- User-Agent
- Timestamp précis (microsecondes)
- Session info (si lié)

#### Response (200 OK)

```typescript
interface SignSimpleResponse {
  data: {
    id: string;
    status: 'partially_signed' | 'completed';
    signers: Signer[];
    signed_at: string;
    signature_log_id: string;     // ID du log créé
    document_hash: string;
    signature_hash: string;       // Hash de la signature
    chain_hash: string;           // Hash dans la chaîne
    certificate_info: {
      issuer: string;             // CN du certificat serveur
      serial: string;
      valid_from: string;
      valid_to: string;
    };
  };
  message: string;
}
```

---

### 2.3 Obtenir la piste d'audit

**GET** `/api/v1/signature-requests/{id}/audit-trail`

#### Response (200 OK)

```typescript
interface AuditTrailResponse {
  data: {
    signature_request: {
      id: string;
      document_id: string;
      created_at: string;
      completed_at: string | null;
    };
    document: {
      original_filename: string;
      hash: string;
      size_bytes: number;
    };
    events: SignatureLogEvent[];
    chain_valid: boolean;         // Intégrité de la chaîne
  };
}

interface SignatureLogEvent {
  id: string;
  sequence_number: number;
  action: 'request_created' | 'signing_initiated' | 'consent_given' |
          'signature_captured' | 'signature_certified' | 'completed' | 'error';
  actor: {
    type: 'system' | 'patient' | 'practitioner';
    name: string;
    id: string;
  };
  metadata: {
    ip_address: string;
    user_agent: string;
    timestamp: string;            // ISO-8601 avec microsecondes
    document_hash?: string;
    signature_hash?: string;
    certificate?: CertificateInfo;
  };
  current_hash: string;
  previous_hash: string | null;
  created_at: string;
}
```

---

### 2.4 Vérifier l'intégrité

**POST** `/api/v1/signature-requests/{id}/verify-integrity`

#### Response (200 OK)

```typescript
interface VerifyIntegrityResponse {
  data: {
    chain_valid: boolean;
    document_valid: boolean;      // Hash document inchangé
    signature_valid: boolean;     // Signatures non altérées
    events_count: number;
    first_event: string;          // Timestamp
    last_event: string;
    issues: string[];             // Problèmes détectés
  };
  message: string;
}
```

---

## 3. Modèle SignatureLog (Hash Chain)

### 3.1 Schema SQL

```sql
CREATE TABLE signature_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id),
    signature_request_id UUID NOT NULL REFERENCES signature_requests(id),

    -- Séquence et chaîne
    sequence_number INTEGER NOT NULL,
    previous_hash VARCHAR(64),
    current_hash VARCHAR(64) NOT NULL UNIQUE,

    -- Action
    action VARCHAR(50) NOT NULL,
    -- request_created, signing_initiated, consent_given,
    -- signature_captured, signature_certified, completed, error

    -- Acteur
    actor_type VARCHAR(20) NOT NULL,  -- system, patient, practitioner
    actor_id UUID,
    actor_name VARCHAR(255),

    -- Métadonnées probantes
    ip_address INET,
    user_agent TEXT,
    action_timestamp TIMESTAMP(6) WITH TIME ZONE NOT NULL,  -- Microsecondes

    -- Données signature (si applicable)
    document_hash VARCHAR(64),
    signature_image_hash VARCHAR(64),
    consent_text TEXT,
    consent_confirmed BOOLEAN,

    -- Certificat (pour simple)
    certificate_serial VARCHAR(100),
    certificate_issuer VARCHAR(500),
    certificate_thumbprint VARCHAR(64),

    -- Données OpenAPI (pour eIDAS)
    openapi_signer_data JSONB,
    openapi_certificate JSONB,
    openapi_timestamp JSONB,

    -- Session/Acte lié
    session_id UUID REFERENCES sessions(id),
    act_name VARCHAR(255),
    practitioner_name VARCHAR(255),

    -- Erreur
    error_message TEXT,

    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),

    -- Contraintes
    CONSTRAINT signature_logs_sequence_unique
        UNIQUE (signature_request_id, sequence_number),
    CONSTRAINT signature_logs_chain_valid
        CHECK (sequence_number = 1 OR previous_hash IS NOT NULL)
);

-- Index pour requêtes fréquentes
CREATE INDEX idx_signature_logs_request ON signature_logs(signature_request_id);
CREATE INDEX idx_signature_logs_actor ON signature_logs(actor_type, actor_id);
CREATE INDEX idx_signature_logs_action ON signature_logs(action);
```

### 3.2 Calcul du Hash

```php
// App\Models\SignatureLog
protected function calculateHash(array $data, ?string $previousHash): string
{
    $hashData = [
        'signature_request_id' => $data['signature_request_id'],
        'sequence_number' => $data['sequence_number'],
        'action' => $data['action'],
        'actor_type' => $data['actor_type'],
        'actor_id' => $data['actor_id'],
        'actor_name' => $data['actor_name'],
        'ip_address' => $data['ip_address'],
        'user_agent' => $data['user_agent'],
        'action_timestamp' => $data['action_timestamp'],
        'document_hash' => $data['document_hash'] ?? null,
        'signature_image_hash' => $data['signature_image_hash'] ?? null,
        'consent_confirmed' => $data['consent_confirmed'] ?? null,
        'previous_hash' => $previousHash,
    ];

    return hash('sha256', json_encode($hashData, JSON_UNESCAPED_UNICODE));
}
```

### 3.3 Immutabilité

```php
// App\Models\SignatureLog
protected static function booted(): void
{
    static::updating(function () {
        throw new \RuntimeException(
            'SignatureLog records are immutable and cannot be modified.'
        );
    });

    static::deleting(function () {
        throw new \RuntimeException(
            'SignatureLog records are immutable and cannot be deleted.'
        );
    });
}
```

---

## 4. Flux eIDAS avec Thème

### 4.1 Appel OpenAPI

```php
// App\Services\Signature\OpenApiESignatureService
public function createEidasRequest(
    SignatureRequest $request,
    Document $document,
    array $signers,
    ?array $uiCustomization = null
): array {
    $payload = [
        'inputDocuments' => [
            [
                'sourceType' => 'base64',
                'payload' => base64_encode($document->getContent()),
            ]
        ],
        'signers' => $this->buildOpenApiSigners($signers),
        'callback' => [
            'url' => route('api.v1.webhooks.openapi-signature'),
            'method' => 'JSON',
            'custom' => [
                'signature_request_id' => $request->id,
                'tenant_id' => $request->tenant_id,
            ],
        ],
        'options' => $this->buildUiOptions($uiCustomization),
    ];

    return $this->client->post('/EU-SES', $payload);
}

private function buildUiOptions(?array $customization): array
{
    $tenant = Tenant::find(auth()->user()->tenant_id);
    $defaults = $tenant->signature_branding ?? [];

    return [
        'ui' => [
            // Sidebar
            'sidebarBackgroundColor' => $customization['secondary_color']
                ?? $defaults['secondary_color'] ?? '#f5f5f5',
            'sidebarLogo' => $customization['logo_url']
                ?? $defaults['logo_url'] ?? null,

            // Header
            'headerBackgroundColor' => $customization['header_color']
                ?? $defaults['header_color'] ?? '#ffffff',

            // Boutons
            'buttonBackgroundColor' => $customization['primary_color']
                ?? $defaults['primary_color'] ?? '#10b981',
            'signButtonBackgroundColor' => $customization['primary_color']
                ?? $defaults['primary_color'] ?? '#10b981',

            // Texte
            'sidebarTextColor' => $customization['text_color']
                ?? $defaults['text_color'] ?? '#374151',

            // Redirections
            'completeUrl' => $customization['complete_url']
                ?? route('signature.complete'),
            'cancelUrl' => $customization['cancel_url']
                ?? route('signature.cancelled'),

            // Options
            'hideSidebar' => $customization['hide_sidebar'] ?? false,
            'hideHeader' => $customization['hide_header'] ?? false,
        ],
        'signatureMode' => ['drawn', 'typed'],
    ];
}
```

---

## 5. Flux Simple avec Certificat Serveur

### 5.1 Process de signature

```php
// App\Services\Signature\SimpleSignatureService
public function signDocument(
    SignatureRequest $request,
    string $signerType,
    string $signatureImageBase64,
    bool $consentConfirmed
): SignatureRequest {
    // 1. Valider consentement
    if (!$consentConfirmed) {
        throw new SignatureException('Consent must be confirmed');
    }

    // 2. Logger consentement
    $this->logEvent($request, 'consent_given', $signerType, [
        'consent_text' => $request->consent_text,
        'consent_confirmed' => true,
    ]);

    // 3. Valider et hasher l'image
    $signatureHash = hash('sha256', base64_decode($signatureImageBase64));

    // 4. Logger capture signature
    $this->logEvent($request, 'signature_captured', $signerType, [
        'signature_image_hash' => $signatureHash,
    ]);

    // 5. Appliquer signature sur PDF
    $signedPdf = $this->pdfService->applySignature(
        $request->document,
        $signatureImageBase64,
        $signerType
    );

    // 6. Certifier avec certificat serveur
    $certifiedPdf = $this->certifyWithServerCertificate($signedPdf);

    // 7. Logger certification
    $this->logEvent($request, 'signature_certified', $signerType, [
        'document_hash' => hash('sha256', $certifiedPdf),
        'certificate_serial' => $this->serverCertificate->getSerialNumber(),
        'certificate_issuer' => $this->serverCertificate->getIssuerDN(),
        'certificate_thumbprint' => $this->serverCertificate->getThumbprint(),
    ]);

    // 8. Stocker le PDF signé (chiffré)
    $this->storeSignedDocument($request, $certifiedPdf);

    // 9. Mettre à jour status
    $this->updateSignerStatus($request, $signerType);

    return $request->fresh();
}

private function certifyWithServerCertificate(string $pdfContent): string
{
    $certificate = $this->loadServerCertificate();
    $privateKey = $this->loadServerPrivateKey();

    // Utiliser TCPDF ou SetaPDF pour signature PDF
    return $this->pdfSigner->sign(
        $pdfContent,
        $certificate,
        $privateKey,
        [
            'Name' => config('app.name'),
            'Location' => 'Server',
            'Reason' => 'Document signature certification',
            'ContactInfo' => config('app.url'),
        ]
    );
}
```

### 5.2 Texte de consentement par défaut

```php
// config/signature.php
return [
    'consent_text' => [
        'fr' => "Je confirme avoir été informé(e) par le praticien de la nature de l'acte, de ses risques éventuels et des alternatives possibles. J'ai pu poser mes questions et obtenir des réponses satisfaisantes. Je consens librement à cet acte.",
        'en' => "I confirm that I have been informed by the practitioner of the nature of the procedure, its potential risks and possible alternatives. I was able to ask my questions and obtain satisfactory answers. I freely consent to this procedure.",
        'he' => "אני מאשר/ת שהמטפל יידע אותי על מהות הפעולה, הסיכונים האפשריים והחלופות הקיימות. יכולתי לשאול שאלות ולקבל תשובות מספקות. אני מסכים/ה לפעולה זו מרצוני החופשי.",
    ],

    'server_certificate' => [
        'path' => env('SIGNATURE_CERT_PATH', storage_path('certs/server.pem')),
        'key_path' => env('SIGNATURE_KEY_PATH', storage_path('certs/server.key')),
        'key_password' => env('SIGNATURE_KEY_PASSWORD'),
    ],
];
```

---

## 6. Exemples cURL

### 6.1 Créer signature simple

```bash
curl -X POST https://api.praticonnect.com/api/v1/signature-requests \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "document_id": "550e8400-e29b-41d4-a716-446655440000",
    "signature_type": "simple",
    "signers": [
      {"type": "patient"},
      {"type": "practitioner"}
    ],
    "session_id": "660e8400-e29b-41d4-a716-446655440001"
  }'
```

### 6.2 Créer signature eIDAS avec thème

```bash
curl -X POST https://api.praticonnect.com/api/v1/signature-requests \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "document_id": "550e8400-e29b-41d4-a716-446655440000",
    "signature_type": "eidas",
    "signers": [
      {
        "type": "patient",
        "email": "patient@example.com",
        "mobile": "+33612345678",
        "authentication": ["email", "sms"]
      }
    ],
    "ui_customization": {
      "primary_color": "#10b981",
      "secondary_color": "#f0fdf4",
      "logo_url": "https://cdn.praticonnect.com/logos/practitioner-123.png",
      "complete_url": "https://praticonnect.com/patient/signature/complete"
    }
  }'
```

### 6.3 Signer avec pad simple

```bash
curl -X POST https://api.praticonnect.com/api/v1/signature-requests/$ID/sign-simple \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "signature_image": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...",
    "consent_confirmed": true,
    "signer_type": "patient"
  }'
```

### 6.4 Obtenir audit trail

```bash
curl -X GET https://api.praticonnect.com/api/v1/signature-requests/$ID/audit-trail \
  -H "Authorization: Bearer $TOKEN"
```

---

## 7. Fichiers à créer/modifier

### 7.1 Nouveaux fichiers

| Fichier | Description |
|---------|-------------|
| `app/Models/SignatureLog.php` | Modèle hash chain immuable |
| `database/migrations/xxxx_create_signature_logs_table.php` | Migration |
| `app/Services/Signature/SignatureAuditService.php` | Service logging |
| `app/Services/Signature/SimpleSignatureService.php` | Service signature simple |
| `app/Services/Signature/PdfSignerService.php` | Service certification PDF |
| `app/Http/Controllers/Api/V1/SignatureAuditController.php` | Controller audit |
| `config/signature.php` | Configuration |

### 7.2 Fichiers à modifier

| Fichier | Modification |
|---------|-------------|
| `app/Services/Signature/OpenApiESignatureService.php` | Ajouter UI customization |
| `app/Http/Controllers/Api/V1/SignatureController.php` | Ajouter type selection |
| `app/Models/SignatureRequest.php` | Ajouter signature_type |
| `routes/api.php` | Nouvelles routes |

---

## 8. Frontend Components

### 8.1 SignatureTypeSelector

```typescript
// frontend/src/components/signature/SignatureTypeSelector.tsx
interface Props {
  onSelect: (type: 'simple' | 'eidas') => void;
  eidasCredits: number;
}
```

### 8.2 SimpleSignaturePad

```typescript
// frontend/src/components/signature/SimpleSignaturePad.tsx
interface Props {
  consentText: string;
  onSign: (signatureImage: string, consentConfirmed: boolean) => void;
  patientName: string;
  practitionerName: string;
  actName: string;
}
```

### 8.3 SignatureAuditTrail

```typescript
// frontend/src/components/signature/SignatureAuditTrail.tsx
interface Props {
  signatureRequestId: string;
}
```

---

## 9. Installation et Prérequis

### 9.1 Dépendances système (serveur)

Les outils suivants sont nécessaires pour la génération et fusion des PDF :

```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install -y wkhtmltopdf ghostscript pdftk-java

# Alternative: pdftk seul (si ghostscript pose problème)
sudo apt-get install -y wkhtmltopdf pdftk-java

# macOS (avec Homebrew)
brew install wkhtmltopdf ghostscript
brew install pdftk-java  # ou via: brew cask install pdftk-java
```

| Outil | Usage | Obligatoire |
|-------|-------|-------------|
| `wkhtmltopdf` | Conversion HTML → PDF (Snappy) | ✅ Oui |
| `pdftk` ou `ghostscript` | Fusion de PDF (ajout signature) | ✅ Au moins un |
| OpenSSL | Certification X.509 | ✅ Oui (inclus PHP) |

### 9.2 Certificat serveur (Let's Encrypt)

**Par défaut**, le système utilise le certificat Let's Encrypt du serveur.

```bash
# Chemins Let's Encrypt standards (Ploi/Certbot)
/etc/letsencrypt/live/{domain}/fullchain.pem  # Certificat + chaîne
/etc/letsencrypt/live/{domain}/privkey.pem    # Clé privée
```

**Configuration automatique** : Ajoutez simplement `APP_DOMAIN` dans `.env` :

```env
APP_DOMAIN=praticonnect.com
# Les chemins Let's Encrypt sont utilisés automatiquement
```

**Permissions** : L'utilisateur PHP (www-data) doit avoir accès aux certificats :

```bash
# Vérifier l'accès (sur le serveur)
sudo -u www-data cat /etc/letsencrypt/live/praticonnect.com/fullchain.pem | head -5

# Si permission refusée, ajouter www-data au groupe ssl-cert (Ploi le fait automatiquement)
sudo usermod -aG ssl-cert www-data
```

**Développement local** (certificat auto-signé) :

```bash
mkdir -p storage/certs
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout storage/certs/server.key \
  -out storage/certs/server.pem \
  -subj "/CN=PratiConnect Dev/O=PratiConnect/C=FR"

chmod 600 storage/certs/server.key
chmod 644 storage/certs/server.pem
```

Puis dans `.env.local` :
```env
SIGNATURE_CERT_PATH="${APP_ROOT}/storage/certs/server.pem"
SIGNATURE_KEY_PATH="${APP_ROOT}/storage/certs/server.key"
```

### 9.3 Variables d'environnement

Ajoutez ces variables à votre `.env` :

```env
# Certificat pour signature simple
SIGNATURE_CERT_PATH="${APP_ROOT}/storage/certs/server.pem"
SIGNATURE_KEY_PATH="${APP_ROOT}/storage/certs/server.key"
SIGNATURE_KEY_PASSWORD=

# OpenAPI eIDAS (signature qualifiée)
OPENAPI_ESIGNATURE_URL=https://api.open-api.fr
OPENAPI_ESIGNATURE_KEY=votre_cle_api
OPENAPI_WEBHOOK_SECRET=votre_secret_webhook

# Activer/désactiver eIDAS
EIDAS_SIGNATURE_ENABLED=true
EIDAS_COST_PER_SIGNATURE=1
```

### 9.4 Dépendances Composer (PHP)

```bash
# Vérifier que Snappy est installé
composer show barryvdh/laravel-snappy

# Si non installé
composer require barryvdh/laravel-snappy
```

### 9.5 Dépendances npm (Frontend)

```bash
cd frontend

# Pad de signature
pnpm add react-signature-canvas
pnpm add -D @types/react-signature-canvas
```

### 9.6 Migration de la base de données

```bash
# Exécuter les migrations
php artisan migrate

# Vérifier les tables créées
php artisan tinker --execute="Schema::hasTable('signature_logs')"
```

### 9.7 Vérification de l'installation

```bash
# Vérifier wkhtmltopdf
which wkhtmltopdf && wkhtmltopdf --version

# Vérifier pdftk ou ghostscript
which pdftk || which gs

# Vérifier le certificat
openssl x509 -in storage/certs/server.pem -text -noout | head -20

# Tester la configuration Laravel
php artisan config:show signature
```

---

## 10. Troubleshooting

### PDF ne se fusionne pas

Si les PDF ne se fusionnent pas correctement :

1. Vérifiez que `pdftk` ou `ghostscript` est installé
2. Consultez les logs Laravel : `storage/logs/laravel.log`
3. Testez manuellement :
   ```bash
   pdftk doc1.pdf doc2.pdf cat output merged.pdf
   # ou
   gs -dBATCH -dNOPAUSE -q -sDEVICE=pdfwrite -sOutputFile=merged.pdf doc1.pdf doc2.pdf
   ```

### Certificat non trouvé

```bash
# Vérifier les chemins
php artisan tinker --execute="config('signature.server_certificate.path')"

# Vérifier les permissions
ls -la storage/certs/
```

### Snappy ne fonctionne pas

```bash
# Vérifier la configuration Snappy
php artisan config:show snappy

# Tester wkhtmltopdf directement
echo "<h1>Test</h1>" | wkhtmltopdf - test.pdf
```

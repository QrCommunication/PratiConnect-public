# PratiConnect - API Documents

Documentation complète de l'API de gestion des documents avec exemples en plusieurs langues.

## Table des matières

1. [Vue d'ensemble](#vue-densemble)
2. [Endpoints Documents](#endpoints-documents)
3. [Endpoints Templates](#endpoints-templates)
4. [Exemples de Requêtes](#exemples-de-requêtes)

---

## Vue d'ensemble

L'API Documents permet de :
- ✅ Uploader des documents vers le stockage S3
- ✅ Récupérer et télécharger des documents
- ✅ Partager des documents avec les patients
- ✅ Gérer des modèles de documents avec variables dynamiques
- ✅ Générer des documents à partir de templates

**Base URL**: `https://praticonnect.com/api/v1`

**Authentication**: Bearer Token (Sanctum)

```
Authorization: Bearer {votre_token}
```

---

## Endpoints Documents

### 1. Liste des documents

```
GET /documents
```

**Paramètres query**:
- `patient_id` (uuid, optionnel) - Filtrer par patient
- `session_id` (uuid, optionnel) - Filtrer par session
- `document_type` (string, optionnel) - Type: `attachment`, `signed_document`, `invoice`, `report`, `prescription`, `other`
- `shared_only` (boolean, optionnel) - Uniquement les documents partagés
- `page` (integer, optionnel) - Numéro de page (défaut: 1)
- `per_page` (integer, optionnel) - Éléments par page (défaut: 15)

**Exemples**:

<details>
<summary>📘 cURL (Français)</summary>

```bash
curl -X GET "https://praticonnect.com/api/v1/documents?patient_id=550e8400-e29b-41d4-a716-446655440000&per_page=20" \
  -H "Authorization: Bearer votre_token_ici" \
  -H "Accept: application/json"
```
</details>

<details>
<summary>🐘 PHP</summary>

```php
<?php
$client = new \GuzzleHttp\Client();

$response = $client->request('GET', 'https://praticonnect.com/api/v1/documents', [
    'query' => [
        'patient_id' => '550e8400-e29b-41d4-a716-446655440000',
        'per_page' => 20
    ],
    'headers' => [
        'Authorization' => 'Bearer votre_token_ici',
        'Accept' => 'application/json',
    ],
]);

$documents = json_decode($response->getBody(), true);
print_r($documents);
```
</details>

<details>
<summary>🟨 JavaScript (Fetch)</summary>

```javascript
const fetchDocuments = async () => {
  const response = await fetch('https://praticonnect.com/api/v1/documents?patient_id=550e8400-e29b-41d4-a716-446655440000&per_page=20', {
    method: 'GET',
    headers: {
      'Authorization': 'Bearer votre_token_ici',
      'Accept': 'application/json'
    }
  });

  const documents = await response.json();
  console.log(documents);
};

fetchDocuments();
```
</details>

<details>
<summary>🐍 Python (Requests)</summary>

```python
import requests

url = "https://praticonnect.com/api/v1/documents"
params = {
    "patient_id": "550e8400-e29b-41d4-a716-446655440000",
    "per_page": 20
}
headers = {
    "Authorization": "Bearer votre_token_ici",
    "Accept": "application/json"
}

response = requests.get(url, params=params, headers=headers)
documents = response.json()
print(documents)
```
</details>

**Réponse**:

```json
{
  "data": [
    {
      "id": "660e8400-e29b-41d4-a716-446655440001",
      "patient_id": "550e8400-e29b-41d4-a716-446655440000",
      "session_id": "770e8400-e29b-41d4-a716-446655440002",
      "filename": "550e8400-e29b-41d4-a716-446655440000.pdf",
      "original_filename": "compte-rendu-seance.pdf",
      "mime_type": "application/pdf",
      "size_bytes": 524288,
      "human_file_size": "512 KB",
      "document_type": "report",
      "shared_with_patient": true,
      "is_signed": false,
      "created_at": "2024-12-24T14:30:00Z"
    }
  ],
  "meta": {
    "current_page": 1,
    "total": 42
  }
}
```

---

### 2. Upload un document

```
POST /documents
```

**Content-Type**: `multipart/form-data`

**Paramètres**:
- `file` (file, requis) - Fichier à uploader (max 50MB)
- `patient_id` (uuid, optionnel) - ID du patient
- `session_id` (uuid, optionnel) - ID de la session
- `document_type` (string, requis) - Type de document
- `share_with_patient` (boolean, optionnel) - Partager immédiatement

**Exemples**:

<details>
<summary>📘 cURL (Français)</summary>

```bash
curl -X POST "https://praticonnect.com/api/v1/documents" \
  -H "Authorization: Bearer votre_token_ici" \
  -H "Accept: application/json" \
  -F "file=@/chemin/vers/document.pdf" \
  -F "patient_id=550e8400-e29b-41d4-a716-446655440000" \
  -F "document_type=report" \
  -F "share_with_patient=true"
```
</details>

<details>
<summary>🐘 PHP</summary>

```php
<?php
$client = new \GuzzleHttp\Client();

$response = $client->request('POST', 'https://praticonnect.com/api/v1/documents', [
    'headers' => [
        'Authorization' => 'Bearer votre_token_ici',
        'Accept' => 'application/json',
    ],
    'multipart' => [
        [
            'name' => 'file',
            'contents' => fopen('/chemin/vers/document.pdf', 'r'),
            'filename' => 'document.pdf'
        ],
        [
            'name' => 'patient_id',
            'contents' => '550e8400-e29b-41d4-a716-446655440000'
        ],
        [
            'name' => 'document_type',
            'contents' => 'report'
        ],
        [
            'name' => 'share_with_patient',
            'contents' => 'true'
        ],
    ],
]);

$document = json_decode($response->getBody(), true);
print_r($document);
```
</details>

<details>
<summary>🟨 JavaScript (FormData)</summary>

```javascript
const uploadDocument = async (file) => {
  const formData = new FormData();
  formData.append('file', file);
  formData.append('patient_id', '550e8400-e29b-41d4-a716-446655440000');
  formData.append('document_type', 'report');
  formData.append('share_with_patient', 'true');

  const response = await fetch('https://praticonnect.com/api/v1/documents', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer votre_token_ici',
      'Accept': 'application/json'
    },
    body: formData
  });

  const document = await response.json();
  console.log(document);
};

// Usage avec input file
const fileInput = document.querySelector('input[type="file"]');
uploadDocument(fileInput.files[0]);
```
</details>

<details>
<summary>🐍 Python (Requests)</summary>

```python
import requests

url = "https://praticonnect.com/api/v1/documents"
headers = {
    "Authorization": "Bearer votre_token_ici",
    "Accept": "application/json"
}

files = {
    'file': open('/chemin/vers/document.pdf', 'rb')
}

data = {
    'patient_id': '550e8400-e29b-41d4-a716-446655440000',
    'document_type': 'report',
    'share_with_patient': 'true'
}

response = requests.post(url, headers=headers, files=files, data=data)
document = response.json()
print(document)
```
</details>

**Réponse** (201 Created):

```json
{
  "data": {
    "id": "660e8400-e29b-41d4-a716-446655440001",
    "patient_id": "550e8400-e29b-41d4-a716-446655440000",
    "filename": "550e8400-e29b-41d4-a716-446655440000.pdf",
    "original_filename": "document.pdf",
    "mime_type": "application/pdf",
    "size_bytes": 524288,
    "human_file_size": "512 KB",
    "document_type": "report",
    "shared_with_patient": true,
    "created_at": "2024-12-24T14:30:00Z"
  }
}
```

---

### 3. Télécharger un document

```
GET /documents/{id}/download
```

Génère une URL présignée valide 60 minutes pour télécharger le fichier depuis S3.

**Exemples**:

<details>
<summary>📘 cURL (English)</summary>

```bash
curl -X GET "https://praticonnect.com/api/v1/documents/660e8400-e29b-41d4-a716-446655440001/download" \
  -H "Authorization: Bearer your_token_here" \
  -H "Accept: application/json"
```
</details>

<details>
<summary>🐘 PHP</summary>

```php
<?php
$client = new \GuzzleHttp\Client();

$documentId = '660e8400-e29b-41d4-a716-446655440001';

$response = $client->request('GET', "https://praticonnect.com/api/v1/documents/{$documentId}/download", [
    'headers' => [
        'Authorization' => 'Bearer your_token_here',
        'Accept' => 'application/json',
    ],
]);

$result = json_decode($response->getBody(), true);

// Download the file using the presigned URL
$fileContent = file_get_contents($result['download_url']);
file_put_contents('downloaded-document.pdf', $fileContent);
```
</details>

<details>
<summary>🟨 JavaScript (Axios)</summary>

```javascript
import axios from 'axios';

const downloadDocument = async (documentId) => {
  const { data } = await axios.get(
    `https://praticonnect.com/api/v1/documents/${documentId}/download`,
    {
      headers: {
        'Authorization': 'Bearer your_token_here',
        'Accept': 'application/json'
      }
    }
  );

  // Open download URL in new tab
  window.open(data.download_url, '_blank');
};

downloadDocument('660e8400-e29b-41d4-a716-446655440001');
```
</details>

<details>
<summary>🐍 Python</summary>

```python
import requests

document_id = "660e8400-e29b-41d4-a716-446655440001"
url = f"https://praticonnect.com/api/v1/documents/{document_id}/download"
headers = {
    "Authorization": "Bearer your_token_here",
    "Accept": "application/json"
}

response = requests.get(url, headers=headers)
result = response.json()

# Download the file using the presigned URL
file_response = requests.get(result['download_url'])
with open('downloaded-document.pdf', 'wb') as f:
    f.write(file_response.content)
```
</details>

**Réponse**:

```json
{
  "download_url": "https://storage.example.com/bucket/tenants/.../document.pdf?X-Amz-Expires=3600&X-Amz-Signature=...",
  "expires_in": 3600
}
```

---

### 4. Partager avec le patient

```
POST /documents/{id}/share
```

Rend le document accessible au patient via son portail.

**Exemples**:

<details>
<summary>📘 cURL (עברית)</summary>

```bash
curl -X POST "https://praticonnect.com/api/v1/documents/660e8400-e29b-41d4-a716-446655440001/share" \
  -H "Authorization: Bearer הטוקן_שלך_כאן" \
  -H "Accept: application/json"
```
</details>

<details>
<summary>🐘 PHP</summary>

```php
<?php
$client = new \GuzzleHttp\Client();

$documentId = '660e8400-e29b-41d4-a716-446655440001';

$response = $client->request('POST', "https://praticonnect.com/api/v1/documents/{$documentId}/share", [
    'headers' => [
        'Authorization' => 'Bearer הטוקן_שלך_כאן',
        'Accept' => 'application/json',
    ],
]);

$document = json_decode($response->getBody(), true);
print_r($document);
```
</details>

<details>
<summary>🟨 JavaScript</summary>

```javascript
const shareDocument = async (documentId) => {
  const response = await fetch(
    `https://praticonnect.com/api/v1/documents/${documentId}/share`,
    {
      method: 'POST',
      headers: {
        'Authorization': 'Bearer הטוקן_שלך_כאן',
        'Accept': 'application/json'
      }
    }
  );

  const document = await response.json();
  console.log('מסמך שותף בהצלחה:', document);
};

shareDocument('660e8400-e29b-41d4-a716-446655440001');
```
</details>

<details>
<summary>🐍 Python</summary>

```python
import requests

document_id = "660e8400-e29b-41d4-a716-446655440001"
url = f"https://praticonnect.com/api/v1/documents/{document_id}/share"
headers = {
    "Authorization": "Bearer הטוקן_שלך_כאן",
    "Accept": "application/json"
}

response = requests.post(url, headers=headers)
document = response.json()
print("מסמך שותף בהצלחה:", document)
```
</details>

---

## Endpoints Templates

### 1. Liste des modèles

```
GET /document-templates
```

**Paramètres query**:
- `active_only` (boolean) - Uniquement les modèles actifs
- `include_variables` (boolean) - Inclure les variables disponibles

**Exemples**:

<details>
<summary>📘 cURL</summary>

```bash
curl -X GET "https://praticonnect.com/api/v1/document-templates?active_only=true&include_variables=true" \
  -H "Authorization: Bearer votre_token" \
  -H "Accept: application/json"
```
</details>

<details>
<summary>🟨 JavaScript (React Query)</summary>

```javascript
import { useQuery } from '@tanstack/react-query';

const useDocumentTemplates = () => {
  return useQuery({
    queryKey: ['document-templates'],
    queryFn: async () => {
      const response = await fetch(
        'https://praticonnect.com/api/v1/document-templates?active_only=true',
        {
          headers: {
            'Authorization': `Bearer ${localStorage.getItem('token')}`,
            'Accept': 'application/json'
          }
        }
      );
      return response.json();
    }
  });
};

// Usage dans un composant
function TemplatesList() {
  const { data, isLoading } = useDocumentTemplates();

  if (isLoading) return <div>Chargement...</div>;

  return (
    <ul>
      {data.data.map(template => (
        <li key={template.id}>{template.name}</li>
      ))}
    </ul>
  );
}
```
</details>

---

### 2. Créer un modèle

```
POST /document-templates
```

**Body**:

```json
{
  "name": "Compte-rendu de séance",
  "description": "Modèle standard pour les comptes-rendus",
  "content": "<h1>Compte-rendu de consultation</h1><p><strong>Patient:</strong> {{patient.full_name}}</p><p><strong>Date:</strong> {{session.date}}</p><p><strong>Motif:</strong></p><p>{{custom.motif}}</p><p><strong>Observations:</strong></p><p>{{custom.observations}}</p>",
  "custom_fields": [
    {
      "name": "motif",
      "type": "text",
      "label": {
        "fr": "Motif de la consultation",
        "en": "Consultation reason",
        "he": "סיבת הייעוץ"
      }
    },
    {
      "name": "observations",
      "type": "text",
      "label": {
        "fr": "Observations",
        "en": "Observations",
        "he": "תצפיות"
      }
    }
  ],
  "requires_signature": false,
  "is_active": true
}
```

**Exemples**:

<details>
<summary>🐘 PHP</summary>

```php
<?php
$client = new \GuzzleHttp\Client();

$templateData = [
    'name' => 'Compte-rendu de séance',
    'description' => 'Modèle standard',
    'content' => '<h1>Compte-rendu</h1><p>Patient: {{patient.full_name}}</p>',
    'custom_fields' => [
        [
            'name' => 'motif',
            'type' => 'text',
            'label' => [
                'fr' => 'Motif de la consultation',
                'en' => 'Consultation reason',
                'he' => 'סיבת הייעוץ'
            ]
        ]
    ],
    'requires_signature' => false,
    'is_active' => true
];

$response = $client->request('POST', 'https://praticonnect.com/api/v1/document-templates', [
    'headers' => [
        'Authorization' => 'Bearer votre_token',
        'Accept' => 'application/json',
        'Content-Type' => 'application/json',
    ],
    'json' => $templateData
]);

$template = json_decode($response->getBody(), true);
```
</details>

---

### 3. Générer un document depuis un modèle

```
POST /document-templates/generate
```

**Body**:

```json
{
  "template_id": "550e8400-e29b-41d4-a716-446655440000",
  "patient_id": "660e8400-e29b-41d4-a716-446655440001",
  "session_id": "770e8400-e29b-41d4-a716-446655440002",
  "custom_fields": {
    "motif": "Douleur lombaire persistante",
    "observations": "Amélioration notable de la mobilité cervicale. Patient plus détendu."
  },
  "save_as_document": false
}
```

**Exemples**:

<details>
<summary>🟨 JavaScript (TypeScript)</summary>

```typescript
import axios from 'axios';

interface GenerateDocumentParams {
  templateId: string;
  patientId: string;
  sessionId?: string;
  customFields: Record<string, string>;
  saveAsDocument?: boolean;
}

const generateDocument = async (params: GenerateDocumentParams) => {
  const { data } = await axios.post(
    'https://praticonnect.com/api/v1/document-templates/generate',
    {
      template_id: params.templateId,
      patient_id: params.patientId,
      session_id: params.sessionId,
      custom_fields: params.customFields,
      save_as_document: params.saveAsDocument || false
    },
    {
      headers: {
        'Authorization': `Bearer ${getToken()}`,
        'Accept': 'application/json',
        'Content-Type': 'application/json'
      }
    }
  );

  return data.content; // HTML généré
};

// Usage
const html = await generateDocument({
  templateId: '550e8400-e29b-41d4-a716-446655440000',
  patientId: '660e8400-e29b-41d4-a716-446655440001',
  sessionId: '770e8400-e29b-41d4-a716-446655440002',
  customFields: {
    motif: 'Douleur lombaire persistante',
    observations: 'Amélioration notable'
  }
});

console.log(html);
```
</details>

<details>
<summary>🐍 Python</summary>

```python
import requests

url = "https://praticonnect.com/api/v1/document-templates/generate"
headers = {
    "Authorization": "Bearer your_token",
    "Accept": "application/json",
    "Content-Type": "application/json"
}

payload = {
    "template_id": "550e8400-e29b-41d4-a716-446655440000",
    "patient_id": "660e8400-e29b-41d4-a716-446655440001",
    "session_id": "770e8400-e29b-41d4-a716-446655440002",
    "custom_fields": {
        "motif": "Douleur lombaire persistante",
        "observations": "Amélioration notable de la mobilité"
    },
    "save_as_document": False
}

response = requests.post(url, headers=headers, json=payload)
result = response.json()

# HTML content généré
html_content = result['content']
print(html_content)
```
</details>

**Réponse**:

```json
{
  "content": "<h1>Compte-rendu de consultation</h1><p><strong>Patient:</strong> Marie Dupont</p><p><strong>Date:</strong> 24/12/2024</p><p><strong>Motif:</strong></p><p>Douleur lombaire persistante</p><p><strong>Observations:</strong></p><p>Amélioration notable de la mobilité cervicale. Patient plus détendu.</p>"
}
```

---

## Variables Disponibles

Les modèles peuvent utiliser les variables suivantes :

### Patient
- `{{patient.first_name}}` - Prénom
- `{{patient.last_name}}` - Nom
- `{{patient.full_name}}` - Nom complet
- `{{patient.email}}` - Email
- `{{patient.phone}}` - Téléphone
- `{{patient.birth_date}}` - Date de naissance (format: DD/MM/YYYY)
- `{{patient.age}}` - Âge
- `{{patient.gender}}` - Genre
- `{{patient.address}}` - Adresse complète

### Praticien
- `{{practitioner.first_name}}` - Prénom
- `{{practitioner.last_name}}` - Nom
- `{{practitioner.full_name}}` - Nom complet avec titre
- `{{practitioner.title}}` - Titre (Dr., M., etc.)
- `{{practitioner.specialty}}` - Spécialité
- `{{practitioner.email}}` - Email
- `{{practitioner.phone}}` - Téléphone

### Session
- `{{session.date}}` - Date (format: DD/MM/YYYY)
- `{{session.time}}` - Heure (format: HH:MM)
- `{{session.duration}}` - Durée (ex: "60 minutes")
- `{{session.notes}}` - Notes de séance (texte brut)

### Système
- `{{date}}` - Date du jour
- `{{time}}` - Heure actuelle
- `{{datetime}}` - Date et heure

### Champs personnalisés
- `{{custom.nom_du_champ}}` - Valeur du champ personnalisé

---

## Codes d'erreur

| Code | Description |
|------|-------------|
| 200 | Succès |
| 201 | Créé avec succès |
| 204 | Supprimé avec succès |
| 401 | Non authentifié |
| 403 | Non autorisé |
| 404 | Ressource non trouvée |
| 422 | Erreur de validation |
| 500 | Erreur serveur |

---

## Notes importantes

1. **Taille maximale** : 50 MB par fichier
2. **Formats supportés** : PDF, DOC, DOCX, JPG, JPEG, PNG, GIF, TXT, CSV, XLSX, XLS
3. **Stockage** : S3 compatible avec backup automatique
4. **URLs présignées** : Validité de 60 minutes
5. **Sécurité** : Row Level Security (RLS) automatique par tenant
6. **Chiffrement** : Tous les documents sont stockés de manière sécurisée

---

## Support

Pour toute question ou problème, contactez l'équipe support :
- Email: support@praticonnect.com
- Documentation: https://docs.praticonnect.com

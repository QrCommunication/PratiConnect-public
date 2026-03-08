# PratiConnect - API Questionnaires - Exemples d'utilisation

## Table des matières
- [Français](#français)
- [English](#english)
- [עברית](#עברית)

---

## Français

### Authentification
Tous les endpoints nécessitent un token Bearer. Ajoutez le header :
```
Authorization: Bearer {votre_token}
```

### 1. Créer un questionnaire

#### cURL
```bash
curl -X POST https://praticonnect.com/api/v1/questionnaires \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept-Language: fr" \
  -d '{
    "name": "Évaluation de la douleur",
    "description": "Questionnaire pour évaluer le niveau de douleur du patient",
    "frequency": "recurring",
    "questions": [
      {
        "id": "q1",
        "type": "scale",
        "label": {
          "fr": "Sur une échelle de 0 à 10, quel est votre niveau de douleur actuel?",
          "en": "On a scale of 0 to 10, what is your current pain level?",
          "he": "בסקלה של 0 עד 10, מה רמת הכאב הנוכחית שלך?"
        },
        "required": true,
        "min": 0,
        "max": 10,
        "trackEvolution": true
      },
      {
        "id": "q2",
        "type": "textarea",
        "label": {
          "fr": "Décrivez votre douleur",
          "en": "Describe your pain",
          "he": "תאר את הכאב שלך"
        },
        "required": false,
        "trackEvolution": false
      },
      {
        "id": "q3",
        "type": "multi_choice",
        "label": {
          "fr": "Où ressentez-vous la douleur?",
          "en": "Where do you feel the pain?",
          "he": "איפה אתה מרגיש את הכאב?"
        },
        "required": true,
        "options": [
          {"value": "neck", "label": {"fr": "Cou", "en": "Neck", "he": "צוואר"}},
          {"value": "back", "label": {"fr": "Dos", "en": "Back", "he": "גב"}},
          {"value": "shoulders", "label": {"fr": "Épaules", "en": "Shoulders", "he": "כתפיים"}},
          {"value": "legs", "label": {"fr": "Jambes", "en": "Legs", "he": "רגליים"}}
        ],
        "trackEvolution": false
      }
    ],
    "is_active": true
  }'
```

#### PHP
```php
<?php

$client = new GuzzleHttp\Client([
    'base_uri' => 'https://praticonnect.com',
    'headers' => [
        'Authorization' => 'Bearer ' . $token,
        'Content-Type' => 'application/json',
        'Accept-Language' => 'fr'
    ]
]);

$response = $client->post('/v1/questionnaires', [
    'json' => [
        'name' => 'Évaluation de la douleur',
        'description' => 'Questionnaire pour évaluer le niveau de douleur du patient',
        'frequency' => 'recurring',
        'questions' => [
            [
                'id' => 'q1',
                'type' => 'scale',
                'label' => [
                    'fr' => 'Sur une échelle de 0 à 10, quel est votre niveau de douleur actuel?',
                    'en' => 'On a scale of 0 to 10, what is your current pain level?',
                    'he' => 'בסקלה של 0 עד 10, מה רמת הכאב הנוכחית שלך?'
                ],
                'required' => true,
                'min' => 0,
                'max' => 10,
                'trackEvolution' => true
            ],
            [
                'id' => 'q2',
                'type' => 'textarea',
                'label' => [
                    'fr' => 'Décrivez votre douleur',
                    'en' => 'Describe your pain',
                    'he' => 'תאר את הכאב שלך'
                ],
                'required' => false,
                'trackEvolution' => false
            ],
            [
                'id' => 'q3',
                'type' => 'multi_choice',
                'label' => [
                    'fr' => 'Où ressentez-vous la douleur?',
                    'en' => 'Where do you feel the pain?',
                    'he' => 'איפה אתה מרגיש את הכאב?'
                ],
                'required' => true,
                'options' => [
                    ['value' => 'neck', 'label' => ['fr' => 'Cou', 'en' => 'Neck', 'he' => 'צוואר']],
                    ['value' => 'back', 'label' => ['fr' => 'Dos', 'en' => 'Back', 'he' => 'גב']],
                    ['value' => 'shoulders', 'label' => ['fr' => 'Épaules', 'en' => 'Shoulders', 'he' => 'כתפיים']],
                    ['value' => 'legs', 'label' => ['fr' => 'Jambes', 'en' => 'Legs', 'he' => 'רגליים']]
                ],
                'trackEvolution' => false
            ]
        ],
        'is_active' => true
    ]
]);

$questionnaire = json_decode($response->getBody(), true);
echo "Questionnaire créé avec ID: " . $questionnaire['data']['id'];
```

#### JavaScript (Fetch)
```javascript
// Créer un questionnaire
const createQuestionnaire = async (token) => {
  const response = await fetch('https://praticonnect.com/api/v1/questionnaires', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
      'Accept-Language': 'fr'
    },
    body: JSON.stringify({
      name: 'Évaluation de la douleur',
      description: 'Questionnaire pour évaluer le niveau de douleur du patient',
      frequency: 'recurring',
      questions: [
        {
          id: 'q1',
          type: 'scale',
          label: {
            fr: 'Sur une échelle de 0 à 10, quel est votre niveau de douleur actuel?',
            en: 'On a scale of 0 to 10, what is your current pain level?',
            he: 'בסקלה של 0 עד 10, מה רמת הכאב הנוכחית שלך?'
          },
          required: true,
          min: 0,
          max: 10,
          trackEvolution: true
        },
        {
          id: 'q2',
          type: 'textarea',
          label: {
            fr: 'Décrivez votre douleur',
            en: 'Describe your pain',
            he: 'תאר את הכאב שלך'
          },
          required: false,
          trackEvolution: false
        },
        {
          id: 'q3',
          type: 'multi_choice',
          label: {
            fr: 'Où ressentez-vous la douleur?',
            en: 'Where do you feel the pain?',
            he: 'איפה אתה מרגיש את הכאב?'
          },
          required: true,
          options: [
            {value: 'neck', label: {fr: 'Cou', en: 'Neck', he: 'צוואר'}},
            {value: 'back', label: {fr: 'Dos', en: 'Back', he: 'גב'}},
            {value: 'shoulders', label: {fr: 'Épaules', en: 'Shoulders', he: 'כתפיים'}},
            {value: 'legs', label: {fr: 'Jambes', en: 'Legs', he: 'רגליים'}}
          ],
          trackEvolution: false
        }
      ],
      is_active: true
    })
  });

  const data = await response.json();
  console.log('Questionnaire créé:', data.data.id);
  return data.data;
};
```

#### Python
```python
import requests

def create_questionnaire(token):
    """Créer un questionnaire"""
    url = 'https://praticonnect.com/api/v1/questionnaires'
    headers = {
        'Authorization': f'Bearer {token}',
        'Content-Type': 'application/json',
        'Accept-Language': 'fr'
    }

    payload = {
        'name': 'Évaluation de la douleur',
        'description': 'Questionnaire pour évaluer le niveau de douleur du patient',
        'frequency': 'recurring',
        'questions': [
            {
                'id': 'q1',
                'type': 'scale',
                'label': {
                    'fr': 'Sur une échelle de 0 à 10, quel est votre niveau de douleur actuel?',
                    'en': 'On a scale of 0 to 10, what is your current pain level?',
                    'he': 'בסקלה של 0 עד 10, מה רמת הכאב הנוכחית שלך?'
                },
                'required': True,
                'min': 0,
                'max': 10,
                'trackEvolution': True
            },
            {
                'id': 'q2',
                'type': 'textarea',
                'label': {
                    'fr': 'Décrivez votre douleur',
                    'en': 'Describe your pain',
                    'he': 'תאר את הכאב שלך'
                },
                'required': False,
                'trackEvolution': False
            },
            {
                'id': 'q3',
                'type': 'multi_choice',
                'label': {
                    'fr': 'Où ressentez-vous la douleur?',
                    'en': 'Where do you feel the pain?',
                    'he': 'איפה אתה מרגיש את הכאב?'
                },
                'required': True,
                'options': [
                    {'value': 'neck', 'label': {'fr': 'Cou', 'en': 'Neck', 'he': 'צוואר'}},
                    {'value': 'back', 'label': {'fr': 'Dos', 'en': 'Back', 'he': 'גב'}},
                    {'value': 'shoulders', 'label': {'fr': 'Épaules', 'en': 'Shoulders', 'he': 'כתפיים'}},
                    {'value': 'legs', 'label': {'fr': 'Jambes', 'en': 'Legs', 'he': 'רגליים'}}
                ],
                'trackEvolution': False
            }
        ],
        'is_active': True
    }

    response = requests.post(url, json=payload, headers=headers)
    response.raise_for_status()

    questionnaire = response.json()
    print(f"Questionnaire créé avec ID: {questionnaire['data']['id']}")
    return questionnaire['data']
```

### 2. Soumettre une réponse au questionnaire

#### cURL
```bash
curl -X POST https://praticonnect.com/api/v1/questionnaire-responses \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept-Language: fr" \
  -d '{
    "questionnaire_id": "550e8400-e29b-41d4-a716-446655440000",
    "patient_id": "660e8400-e29b-41d4-a716-446655440001",
    "session_id": "770e8400-e29b-41d4-a716-446655440002",
    "filled_by": "practitioner",
    "answers": {
      "q1": 7,
      "q2": "Douleur aiguë au niveau du bas du dos qui irradie vers la jambe droite",
      "q3": ["back", "legs"]
    }
  }'
```

#### PHP
```php
<?php

$response = $client->post('/v1/questionnaire-responses', [
    'json' => [
        'questionnaire_id' => '550e8400-e29b-41d4-a716-446655440000',
        'patient_id' => '660e8400-e29b-41d4-a716-446655440001',
        'session_id' => '770e8400-e29b-41d4-a716-446655440002',
        'filled_by' => 'practitioner',
        'answers' => [
            'q1' => 7,
            'q2' => 'Douleur aiguë au niveau du bas du dos qui irradie vers la jambe droite',
            'q3' => ['back', 'legs']
        ]
    ]
]);

$questionnaireResponse = json_decode($response->getBody(), true);
echo "Réponse créée avec ID: " . $questionnaireResponse['data']['id'];
```

#### JavaScript
```javascript
const submitResponse = async (token, questionnaireId, patientId, sessionId) => {
  const response = await fetch('https://praticonnect.com/api/v1/questionnaire-responses', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
      'Accept-Language': 'fr'
    },
    body: JSON.stringify({
      questionnaire_id: questionnaireId,
      patient_id: patientId,
      session_id: sessionId,
      filled_by: 'practitioner',
      answers: {
        q1: 7,
        q2: 'Douleur aiguë au niveau du bas du dos qui irradie vers la jambe droite',
        q3: ['back', 'legs']
      }
    })
  });

  const data = await response.json();
  console.log('Réponse créée:', data.data);
  return data.data;
};
```

#### Python
```python
def submit_response(token, questionnaire_id, patient_id, session_id):
    """Soumettre une réponse au questionnaire"""
    url = 'https://praticonnect.com/api/v1/questionnaire-responses'
    headers = {
        'Authorization': f'Bearer {token}',
        'Content-Type': 'application/json',
        'Accept-Language': 'fr'
    }

    payload = {
        'questionnaire_id': questionnaire_id,
        'patient_id': patient_id,
        'session_id': session_id,
        'filled_by': 'practitioner',
        'answers': {
            'q1': 7,
            'q2': 'Douleur aiguë au niveau du bas du dos qui irradie vers la jambe droite',
            'q3': ['back', 'legs']
        }
    }

    response = requests.post(url, json=payload, headers=headers)
    response.raise_for_status()

    response_data = response.json()
    print(f"Réponse créée avec ID: {response_data['data']['id']}")
    return response_data['data']
```

### 3. Obtenir les données d'évolution

#### cURL
```bash
curl -X GET "https://praticonnect.com/api/v1/questionnaire-responses/patient/660e8400-e29b-41d4-a716-446655440001/questionnaire/550e8400-e29b-41d4-a716-446655440000/evolution?locale=fr" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Accept-Language: fr"
```

#### PHP
```php
<?php

$response = $client->get('/v1/questionnaire-responses/patient/660e8400-e29b-41d4-a716-446655440001/questionnaire/550e8400-e29b-41d4-a716-446655440000/evolution', [
    'query' => ['locale' => 'fr']
]);

$evolutionData = json_decode($response->getBody(), true);

foreach ($evolutionData['data'] as $questionEvolution) {
    echo "Question: " . $questionEvolution['question_label'] . "\n";
    echo "Tendance: " . $questionEvolution['statistics']['trend'] . "\n";
    echo "Amélioration: " . $questionEvolution['statistics']['improvement_percentage'] . "%\n";
}
```

#### JavaScript
```javascript
const getEvolutionData = async (token, patientId, questionnaireId, locale = 'fr') => {
  const response = await fetch(
    `https://praticonnect.com/api/v1/questionnaire-responses/patient/${patientId}/questionnaire/${questionnaireId}/evolution?locale=${locale}`,
    {
      headers: {
        'Authorization': `Bearer ${token}`,
        'Accept-Language': locale
      }
    }
  );

  const data = await response.json();

  // Préparer les données pour Recharts
  data.data.forEach(questionEvolution => {
    console.log(`Question: ${questionEvolution.question_label}`);
    console.log(`Tendance: ${questionEvolution.statistics.trend}`);
    console.log(`Amélioration: ${questionEvolution.statistics.improvement_percentage}%`);
    console.log('Points de données:', questionEvolution.data_points);
  });

  return data.data;
};
```

#### Python
```python
def get_evolution_data(token, patient_id, questionnaire_id, locale='fr'):
    """Obtenir les données d'évolution"""
    url = f'https://praticonnect.com/api/v1/questionnaire-responses/patient/{patient_id}/questionnaire/{questionnaire_id}/evolution'
    headers = {
        'Authorization': f'Bearer {token}',
        'Accept-Language': locale
    }
    params = {'locale': locale}

    response = requests.get(url, headers=headers, params=params)
    response.raise_for_status()

    evolution_data = response.json()

    for question_evolution in evolution_data['data']:
        print(f"Question: {question_evolution['question_label']}")
        print(f"Tendance: {question_evolution['statistics']['trend']}")
        print(f"Amélioration: {question_evolution['statistics']['improvement_percentage']}%")
        print(f"Points de données: {len(question_evolution['data_points'])}")

    return evolution_data['data']
```

---

## English

### Authentication
All endpoints require a Bearer token. Add the header:
```
Authorization: Bearer {your_token}
```

### 1. Create a questionnaire

#### cURL
```bash
curl -X POST https://praticonnect.com/api/v1/questionnaires \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept-Language: en" \
  -d '{
    "name": "Pain Assessment",
    "description": "Questionnaire to assess patient pain level",
    "frequency": "recurring",
    "questions": [
      {
        "id": "q1",
        "type": "scale",
        "label": {
          "fr": "Sur une échelle de 0 à 10, quel est votre niveau de douleur actuel?",
          "en": "On a scale of 0 to 10, what is your current pain level?",
          "he": "בסקלה של 0 עד 10, מה רמת הכאב הנוכחית שלך?"
        },
        "required": true,
        "min": 0,
        "max": 10,
        "trackEvolution": true
      }
    ],
    "is_active": true
  }'
```

#### PHP
```php
<?php

$client = new GuzzleHttp\Client([
    'base_uri' => 'https://praticonnect.com',
    'headers' => [
        'Authorization' => 'Bearer ' . $token,
        'Content-Type' => 'application/json',
        'Accept-Language' => 'en'
    ]
]);

$response = $client->post('/v1/questionnaires', [
    'json' => [
        'name' => 'Pain Assessment',
        'description' => 'Questionnaire to assess patient pain level',
        'frequency' => 'recurring',
        'questions' => [
            [
                'id' => 'q1',
                'type' => 'scale',
                'label' => [
                    'fr' => 'Sur une échelle de 0 à 10, quel est votre niveau de douleur actuel?',
                    'en' => 'On a scale of 0 to 10, what is your current pain level?',
                    'he' => 'בסקלה של 0 עד 10, מה רמת הכאב הנוכחית שלך?'
                ],
                'required' => true,
                'min' => 0,
                'max' => 10,
                'trackEvolution' => true
            ]
        ],
        'is_active' => true
    ]
]);

$questionnaire = json_decode($response->getBody(), true);
echo "Questionnaire created with ID: " . $questionnaire['data']['id'];
```

#### JavaScript
```javascript
const createQuestionnaire = async (token) => {
  const response = await fetch('https://praticonnect.com/api/v1/questionnaires', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
      'Accept-Language': 'en'
    },
    body: JSON.stringify({
      name: 'Pain Assessment',
      description: 'Questionnaire to assess patient pain level',
      frequency: 'recurring',
      questions: [
        {
          id: 'q1',
          type: 'scale',
          label: {
            fr: 'Sur une échelle de 0 à 10, quel est votre niveau de douleur actuel?',
            en: 'On a scale of 0 to 10, what is your current pain level?',
            he: 'בסקלה של 0 עד 10, מה רמת הכאב הנוכחית שלך?'
          },
          required: true,
          min: 0,
          max: 10,
          trackEvolution: true
        }
      ],
      is_active: true
    })
  });

  const data = await response.json();
  console.log('Questionnaire created:', data.data.id);
  return data.data;
};
```

#### Python
```python
import requests

def create_questionnaire(token):
    """Create a questionnaire"""
    url = 'https://praticonnect.com/api/v1/questionnaires'
    headers = {
        'Authorization': f'Bearer {token}',
        'Content-Type': 'application/json',
        'Accept-Language': 'en'
    }

    payload = {
        'name': 'Pain Assessment',
        'description': 'Questionnaire to assess patient pain level',
        'frequency': 'recurring',
        'questions': [
            {
                'id': 'q1',
                'type': 'scale',
                'label': {
                    'fr': 'Sur une échelle de 0 à 10, quel est votre niveau de douleur actuel?',
                    'en': 'On a scale of 0 to 10, what is your current pain level?',
                    'he': 'בסקלה של 0 עד 10, מה רמת הכאב הנוכחית שלך?'
                },
                'required': True,
                'min': 0,
                'max': 10,
                'trackEvolution': True
            }
        ],
        'is_active': True
    }

    response = requests.post(url, json=payload, headers=headers)
    response.raise_for_status()

    questionnaire = response.json()
    print(f"Questionnaire created with ID: {questionnaire['data']['id']}")
    return questionnaire['data']
```

---

## עברית

### אימות
כל נקודות הקצה דורשות טוקן Bearer. הוסף את ה-header:
```
Authorization: Bearer {your_token}
```

### 1. צור שאלון

#### cURL
```bash
curl -X POST https://praticonnect.com/api/v1/questionnaires \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept-Language: he" \
  -d '{
    "name": "הערכת כאב",
    "description": "שאלון להערכת רמת הכאב של המטופל",
    "frequency": "recurring",
    "questions": [
      {
        "id": "q1",
        "type": "scale",
        "label": {
          "fr": "Sur une échelle de 0 à 10, quel est votre niveau de douleur actuel?",
          "en": "On a scale of 0 to 10, what is your current pain level?",
          "he": "בסקלה של 0 עד 10, מה רמת הכאב הנוכחית שלך?"
        },
        "required": true,
        "min": 0,
        "max": 10,
        "trackEvolution": true
      }
    ],
    "is_active": true
  }'
```

#### PHP
```php
<?php

$client = new GuzzleHttp\Client([
    'base_uri' => 'https://praticonnect.com',
    'headers' => [
        'Authorization' => 'Bearer ' . $token,
        'Content-Type' => 'application/json',
        'Accept-Language' => 'he'
    ]
]);

$response = $client->post('/v1/questionnaires', [
    'json' => [
        'name' => 'הערכת כאב',
        'description' => 'שאלון להערכת רמת הכאב של המטופל',
        'frequency' => 'recurring',
        'questions' => [
            [
                'id' => 'q1',
                'type' => 'scale',
                'label' => [
                    'fr' => 'Sur une échelle de 0 à 10, quel est votre niveau de douleur actuel?',
                    'en' => 'On a scale of 0 to 10, what is your current pain level?',
                    'he' => 'בסקלה של 0 עד 10, מה רמת הכאב הנוכחית שלך?'
                ],
                'required' => true,
                'min' => 0,
                'max' => 10,
                'trackEvolution' => true
            ]
        ],
        'is_active' => true
    ]
]);

$questionnaire = json_decode($response->getBody(), true);
echo "שאלון נוצר עם ID: " . $questionnaire['data']['id'];
```

#### JavaScript
```javascript
const createQuestionnaire = async (token) => {
  const response = await fetch('https://praticonnect.com/api/v1/questionnaires', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
      'Accept-Language': 'he'
    },
    body: JSON.stringify({
      name: 'הערכת כאב',
      description: 'שאלון להערכת רמת הכאב של המטופל',
      frequency: 'recurring',
      questions: [
        {
          id: 'q1',
          type: 'scale',
          label: {
            fr: 'Sur une échelle de 0 à 10, quel est votre niveau de douleur actuel?',
            en: 'On a scale of 0 to 10, what is your current pain level?',
            he: 'בסקלה של 0 עד 10, מה רמת הכאב הנוכחית שלך?'
          },
          required: true,
          min: 0,
          max: 10,
          trackEvolution: true
        }
      ],
      is_active: true
    })
  });

  const data = await response.json();
  console.log('שאלון נוצר:', data.data.id);
  return data.data;
};
```

#### Python
```python
import requests

def create_questionnaire(token):
    """צור שאלון"""
    url = 'https://praticonnect.com/api/v1/questionnaires'
    headers = {
        'Authorization': f'Bearer {token}',
        'Content-Type': 'application/json',
        'Accept-Language': 'he'
    }

    payload = {
        'name': 'הערכת כאב',
        'description': 'שאלון להערכת רמת הכאב של המטופל',
        'frequency': 'recurring',
        'questions': [
            {
                'id': 'q1',
                'type': 'scale',
                'label': {
                    'fr': 'Sur une échelle de 0 à 10, quel est votre niveau de douleur actuel?',
                    'en': 'On a scale of 0 to 10, what is your current pain level?',
                    'he': 'בסקלה של 0 עד 10, מה רמת הכאב הנוכחית שלך?'
                },
                'required': True,
                'min': 0,
                'max': 10,
                'trackEvolution': True
            }
        ],
        'is_active': True
    }

    response = requests.post(url, json=payload, headers=headers)
    response.raise_for_status()

    questionnaire = response.json()
    print(f"שאלון נוצר עם ID: {questionnaire['data']['id']}")
    return questionnaire['data']
```

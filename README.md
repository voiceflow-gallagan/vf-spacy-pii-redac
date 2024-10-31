# SpaCy PII Redaction Service

A Flask-based API service that uses SpaCy's Named Entity Recognition (NER) to identify and redact Personally Identifiable Information (PII) from text.

## What is SpaCy PII?

SpaCy is an open-source library for advanced Natural Language Processing in Python. The service uses SpaCy's NER capabilities to identify sensitive information like:

- Names
- Organizations
- Locations
- Dates
- Money values
- Websites
- Email addresses
- Brands
- And more...

Learn more about SpaCy NER at [SpaCy's Named Entities Documentation](https://spacy.io/usage/linguistic-features#named-entities)

## Getting Started

### Prerequisites
- Docker
- Docker Compose

### Running the Service

1. Clone the repository:
   ```bash
   git clone https://github.com/voiceflow-gallagan/vf-spacy-pii-redac.git
   ```

2. Navigate to the project directory:
   ```bash
   cd vf-spacy-pii-redac
   ```

3. Build and run the Docker container:
   ```bash
   docker-compose up --build
   ```

The service will start on port 5005 by default.

### Configuration

To change the port, create a `.env` file in the root directory:

```
FLASK_PORT=5005
```

## API Endpoints

### POST /redact

Redacts PII from the provided text.

#### Request Body

```json
{
  "text": "John Doe works at Acme Corp in New York. His email is john.doe@acme.com."
}
```

#### Response

```json
{
  "redacted_text": "[REDACTED] works at [REDACTED] in [REDACTED] and can be reached at [REDACTED]"
}
```

#### cURL Example


```bash
curl -X POST \
  http://localhost:5005/redact \
  -H 'Content-Type: application/json' \
  -d '{"text": "John Doe works at Acme Corp in New York. His email is john.doe@acme.com."}'
```


## Implementation Details

The service uses SpaCy's medium-sized English model (`en_core_web_md`) for entity recognition, along with custom matchers for websites, emails, and brand names. The redaction process preserves the original text structure while replacing identified entities with "[REDACTED]".

For reference, I used these code blocks:

```python
# Load the spaCy model
nlp = spacy.load("en_core_web_md")
# Create a custom matchers
matcher = Matcher(nlp.vocab)
website_pattern = [{"LIKE_URL": True}]
matcher.add("WEBSITE", [website_pattern])
email_pattern = [{"LIKE_EMAIL": True}]
matcher.add("EMAIL", [email_pattern])
brand_pattern = [{"POS": "PROPN", "IS_TITLE": True}]
matcher.add("BRAND", [brand_pattern])
```
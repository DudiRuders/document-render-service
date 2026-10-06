# DOCX Render API Service

A lightweight, standalone Node.js microservice designed to handle dynamic document generation. It accepts raw JSON data and a base `.docx` template, maps the data to placeholders, and returns a fully customized document.

## 🎯 Purpose
In enterprise automation, rendering documents directly within an orchestrator (like n8n or Make) can be resource-heavy and limited. This dedicated microservice offloads the rendering process, allowing for complex formatting, image replacements, and custom logic via REST API.

## ⚙️ Core Endpoints
All `POST` endpoints take `multipart/form-data` with a file and a `data` field containing a JSON string.

| Endpoint | File field | `data` | Returns |
| --- | --- | --- | --- |
| `GET /health` | – | – | service status and limits |
| `POST /render` | `template` (.docx) | values for `{{placeholders}}`, loops and an optional base64 image | rendered `.docx` |
| `POST /replace-image` | `docx` (.docx) | `{ "obraz_url": "https://..." }` | `.docx` with the picture whose alt text is `REPLACE_ME` replaced |
| `POST /stamp` | `pdf` (.pdf) | `{ "obraz_url": "https://..." }` | `.pdf` with the image stamped in the top-right corner of page 1 |

Remote images are downloaded with size limits, a timeout and a basic SSRF guard (http/https only, no localhost or private IPs, optional host allowlist).

## ▶️ Run it
```bash
npm install
npm start                      # http://localhost:3001
# or
docker build -t render-docx .
docker run -p 3001:3001 render-docx
```

Optional environment variables: `PORT`, `CORS_ORIGINS`, `MAX_UPLOAD_MB`, `MAX_REMOTE_MB`, `AXIOS_TIMEOUT_MS`, `ALLOWED_IMAGE_HOSTS`.

## 🚀 Usage in Architecture
This service acts as the rendering engine for the larger **[Document Automation Engine](https://github.com/DudiRuders/document-automation-engine)**. An orchestrator (e.g., n8n) sends the raw data and template to this API, receives the finalized `.docx`, and passes it along for further processing and cloud storage. A ready-made client demo lives in **[excel-to-docx-pipeline](https://github.com/DudiRuders/excel-to-docx-pipeline)**.

## Compatibility
Tested on: Windows 10/11 + Node 18/20, Docker Desktop.

## Third-party
This project uses open-source packages, including: Express, Docxtemplater, pdf-lib, axios, multer.

## License
MIT, see [LICENSE](LICENSE).

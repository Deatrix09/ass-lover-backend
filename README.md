# A.S.S. Lover — Backend (RAG Ingestion API)

> FastAPI backend for intelligent web content ingestion and RAG search.

## Overview

The backend serves as the orchestrator and ingest engine for the A.S.S. Lover system. It provides:
- Multimodal web content collection (HTML scraping, deep crawl)
- Document indexing into Qdrant vector database
- RAG search with response generation via LLM
- Authentication and authorization via Keycloak (OIDC/OAuth2)

## Architecture

```
Frontend → nginx → FastAPI (backend)
                       ├── ingestion.py   # Scraping pipeline (httpx + BeautifulSoup)
                       ├── rag.py         # RAG pipeline (LangChain + Qdrant + LLM)
                       ├── auth.py        # Keycloak JWT verification
                       ├── models.py      # SQLAlchemy models (PostgreSQL)
                       └── scheduler.py   # Scheduled tasks
```

## Tech Stack

| Component | Technology |
|---|---|
| Framework | FastAPI + Gunicorn/Uvicorn |
| Database | PostgreSQL (SQLAlchemy) |
| Vector DB | Qdrant |
| Embeddings | HuggingFace `paraphrase-multilingual-MiniLM-L12-v2` (CPU) |
| LLM | e-infra API (`llama-4-scout-17b-16e-instruct`) |
| RAG Pipeline | LangChain |
| Scraping | httpx + BeautifulSoup4 + markdownify |
| Auth | Keycloak (OIDC/OAuth2, JWT RS256) |
| Task Queue | Redis (background tasks) |

## API Endpoints

### Authentication
| Method | Endpoint | Description | Role |
|---|---|---|---|
| GET | `/api/auth/me` | Current user profile | user+ |

### Ingestion
| Method | Endpoint | Description | Role |
|---|---|---|---|
| POST | `/api/ingest` | Trigger URL ingestion | admin |
| GET | `/api/jobs` | List ingestion jobs | admin |
| DELETE | `/api/jobs/{id}` | Delete job and its vectors | admin |
| GET | `/api/jobs/{id}/detail` | Job detail + evidence artifacts | user+ |
| GET | `/api/jobs/{id}/files` | List of extracted files | user+ |
| PUT | `/api/jobs/{id}/resolve` | Resolve incident (CAPTCHA/FAILED) | admin |

### Sources
| Method | Endpoint | Description | Role |
|---|---|---|---|
| GET | `/api/sources` | List registered sources | user+ |
| DELETE | `/api/sources/{id}` | Delete source | admin |

### Search
| Method | Endpoint | Description | Role |
|---|---|---|---|
| POST | `/api/search` | RAG search + response generation | optional |

## Installation & Setup

### Requirements
- Python 3.11+
- PostgreSQL
- Qdrant
- Redis
- Keycloak

### Local Development

1. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

2. Configure environment:
   Copy `.env.example` to `.env` and update the values.
   ```bash
   cp .env.example .env
   ```

3. Run the application:
   ```bash
   uvicorn main:app --reload
   ```

### Docker Deployment

Refer to the [ass-lover-infra](https://github.com/Deatrix09/ass-lover-infra) repository for production deployment instructions.

## Environment Variables

Refer to `.env.example` for the full list of supported variables.

| Variable | Description | Default |
|---|---|---|
| `DATABASE_URL` | PostgreSQL connection string | `sqlite:///./rag_storage.db` |
| `QDRANT_HOST` | Qdrant hostname | `localhost` |
| `OLLAMA_URL` | LLM API base URL | — |
| `OLLAMA_API_KEY` | LLM API Key | — |
| `CORS_ORIGINS` | Allowed CORS origins | `http://localhost` |

## License

MIT

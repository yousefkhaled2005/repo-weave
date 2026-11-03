# RepoWeave Backend

**Product:** RepoWeave  
**Component:** Backend Core API & Ingestion Engine  
**Runtime:** Python 3.11 / FastAPI / SQLAlchemy 2.0 Async / pgvector  
**Dependency Management:** [uv](https://docs.astral.sh/uv/)  

---

## 1. Overview

The RepoWeave backend provides:
- Multi-language AST code parsing via Tree-sitter (Python, JavaScript, TypeScript, Go, C, C++).
- Dense vector embedding generation via ONNX FastEmbed or Google Gemini API.
- Dense vector similarity and hybrid search via PostgreSQL `pgvector`.
- Codebase dependency graph extraction and blast radius analysis.
- Multi-step ReAct AI agent orchestration using Groq Cloud LPUs.
- Automated Pull Request review and repository documentation synthesis.

---

## 2. Local Development

### 2.1 Prerequisites
- Python 3.11
- [uv](https://docs.astral.sh/uv/) installed
- PostgreSQL 15+ with `pgvector` enabled

### 2.2 Setup Commands

```bash
# Copy environment configuration
cp .env.example .env

# Install dependencies into virtualenv managed by uv
uv sync --extra dev

# Run database migrations
uv run alembic upgrade head

# Start development server
uv run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

Interactive API documentation will be available at:
- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`

---

## 3. Environment Configuration

Refer to `.env.example` for the complete list of settings. Key production variables:

| Variable | Description | Default / Example |
|---|---|---|
| `ENV` | Environment mode (`development` or `production`) | `development` |
| `DATABASE_URL` | Asyncpg PostgreSQL connection URL | `postgresql+asyncpg://...` |
| `SECRET_KEY` | 32+ character random string for JWT signing | - |
| `GROQ_API_KEY` | Groq Cloud API key for AI agent inference | `gsk_...` |
| `GITHUB_TOKEN` | GitHub Personal Access Token (for higher API rate limits) | `ghp_...` |
| `EMBEDDING_PROVIDER` | Embedding provider (`local` or `google`) | `local` |
| `GOOGLE_API_KEY` | Google Gemini API key (required if `EMBEDDING_PROVIDER=google`) | `AIza...` |
| `CORS_ORIGINS` | Allowed origins JSON array or comma-separated list | `["http://localhost:3000"]` |

---

## 4. Testing & Code Quality

```bash
# Execute test suite
uv run pytest

# Format code
uv run black app tests

# Sort imports
uv run isort app tests

# Lint codebase
uv run flake8 app tests
```

---

## 5. Deployment

The backend includes a production-ready `Dockerfile` and Render blueprint in `infrastructure/render.yaml`.
- Health check path: `/health/ready`
- Pre-deploy command: `uv run alembic upgrade head`
- Recommended embedding provider in constrained cloud environments: `EMBEDDING_PROVIDER=google` with `gemini-embedding-001`.

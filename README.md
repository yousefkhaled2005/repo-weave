# RepoWeave

<div align="center">

**Understand. Connect. Engineer.**

*AI-Powered Repository Intelligence Platform for Deep Code Understanding, Dependency Analysis, and Semantic Engineering Workflows.*

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square)](LICENSE)
[![Python: 3.11](https://img.shields.io/badge/Python-3.11-3776AB.svg?style=flat-square&logo=python&logoColor=white)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.138+-009688.svg?style=flat-square&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![Next.js: 16](https://img.shields.io/badge/Next.js-16-black.svg?style=flat-square&logo=next.js&logoColor=white)](https://nextjs.org/)
[![React: 19](https://img.shields.io/badge/React-19-61DAFB.svg?style=flat-square&logo=react&logoColor=black)](https://react.dev/)
[![PostgreSQL: 16 + pgvector](https://img.shields.io/badge/PostgreSQL-16%20%2B%20pgvector-336791.svg?style=flat-square&logo=postgresql&logoColor=white)](https://github.com/pgvector/pgvector)
[![Tailwind CSS: v4](https://img.shields.io/badge/Tailwind-v4-38B2AC.svg?style=flat-square&logo=tailwind-css&logoColor=white)](https://tailwindcss.com/)

[**Documentation**](docs/architecture.md) • [**API Reference**](docs/api.md) • [**Installation Guide**](docs/installation.md) • [**Roadmap**](docs/roadmap.md) • [**Contributing**](CONTRIBUTING.md)

---
</div>

## 1. Overview

As codebases grow to hundreds of thousands of lines across distributed modules, engineering teams encounter a critical cognitive bottleneck: **the repository understanding gap**. Answering questions like *"What breaks if I change this interface?"*, *"Where is this domain entity transformed across boundaries?"*, or *"How does this pull request impact downstream consumers?"* requires manual cross-file tracing, fragmented keyword searches, and fragile mental models.

**RepoWeave** is a production-grade software engineering intelligence platform engineered to bridge this gap. By combining **Tree-sitter concrete syntax tree (CST) parsing**, **topological import graphs**, **dense pgvector embeddings**, and **hierarchical multi-agent RAG planning**, RepoWeave transforms raw Git repositories into an interactive, context-aware intelligence network.

### Core Value Proposition

- **Deterministic Structural Awareness:** Unlike naive LLM code assistants that ingest flat file buffers, RepoWeave parses language grammars directly into structured AST symbols, function signatures, class hierarchies, and call dependencies.
- **Topological Impact & Blast-Radius Querying:** Graph algorithms trace transitive imports, callers, and callees across modules, computing the exact blast radius of prospective code modifications.
- **Hybrid Retrieval-Augmented Generation (RAG):** Combines PostgreSQL full-text search (`tsvector` / `tsquery`) with dense vector cosine similarity via `pgvector`, blended with dynamic ranking for high-precision code retrieval.
- **Autonomous Multi-Agent Planning:** A structured agent planner formulates multi-step retrieval strategies across metadata, symbol search, graph traversal, and branch comparisons before generating grounded, cited explanations.
- **Automated Pull Request Reviews & Documentation:** Generates deep, contextual PR audits factoring in git diffs and dependency impact, alongside living architectural documentation generated directly from codebase realities.

---

## 2. Core Features

RepoWeave delivers verified, production-tested capabilities engineered for software architects and development teams:

- **Multi-Language AST Parsing:**
  Native grammar parsing for **Python**, **JavaScript**, **TypeScript**, **Go**, **C**, and **C++** using Tree-sitter. Extracts classes, interfaces, methods, functions, enums, signatures, line boundaries, and hierarchical parent-child relationships.
- **Dependency & Blast-Radius Graph Analysis:**
  Constructs directed dependency networks from AST import declarations. Features interactive visualization powered by `@xyflow/react` and Dagre auto-layout, shortest-path resolution between modules, and transitive blast-radius calculation.
- **Hybrid Semantic & Keyword Search:**
  Dual-engine search indexing combining PostgreSQL full-text search with `pgvector` dense vector embeddings. Supports symbol name matching, language filtering, threshold tuning, and syntax-highlighted code snippet previews.
- **Multi-Agent RAG Codebase Chat:**
  Context-grounded AI conversational interface with deterministic file citations, execution latency metrics, and real-time Server-Sent Events (SSE) streaming. Powered by an autonomous tool planner executing metadata, search, graph, and branch inspection tools.
- **Semantic Code Chunking:**
  AST-aware symbol chunking that preserves semantic boundaries for functions and classes, with sliding window chunking for non-symbol files and diff chunking for pull request reviews.
- **Flexible Embedding Pipelines:**
  Supports dual embedding providers: local zero-cost embedding using FastEmbed (`BAAI/bge-small-en-v1.5`, 384 dimensions) or cloud-managed Google Gemini embeddings (`gemini-embedding-001`).
- **Automated Pull Request Code Review:**
  Comprehensive review engine that analyzes PR diff hunks, checks modified symbol blast radii, and generates structured reviews covering architecture impact, correctness, testing considerations, and security.
- **Living Architectural Documentation:**
  One-click automated generation of System Architecture, High-Level Overview, and Developer Onboarding documentation, stored and versioned directly in PostgreSQL.
- **Enterprise Authentication & RBAC:**
  Secure password hashing with bcrypt, stateless JWT authentication stored in HTTP-only `SameSite=Lax` cookies, user workspace isolation, and an administrative control plane.
- **Asynchronous Ingestion Engine:**
  Non-blocking background job pipeline tracking cloning, scanning, parsing, graph extraction, chunking, and embedding with real-time status updates.

---

## 3. Architecture Overview

RepoWeave employs a decoupled, service-oriented architecture designed for reliability, horizontal scalability, and strict separation of concerns.

### Architectural Diagram

```mermaid
flowchart TD
    subgraph Client ["Client Layer (Next.js 16 + React 19)"]
        UI["Web Console & Dashboard"]
        GraphUI["Interactive Graph Explorer (React Flow)"]
        ChatUI["Agent Chat & Citation Viewer"]
        SearchUI["Hybrid Code Search Drawer"]
    end

    subgraph Gateway ["API & Service Gateway (FastAPI)"]
        Router["API v1 Router (/api/v1)"]
        AuthMiddleware["JWT Cookie Auth & RBAC"]
        JobManager["Background Job Processor"]
    end

    subgraph Ingestion ["Ingestion & Analysis Pipeline"]
        Cloner["Repository Cloner (GitPython)"]
        Parser["Tree-sitter CST Engine (Py/TS/JS/Go/C/C++)"]
        Extractor["Symbol & Dependency Extractor"]
        Chunker["Semantic AST Chunker & Diff Chunker"]
    end

    subgraph Intelligence ["AI & Multi-Agent Engine"]
        Planner["LLM Tool Planner"]
        Executor["Tool Execution Engine"]
        Tools["Tools: Search | Graph | Branch | Metadata"]
        ContextBuilder["Context Builder & Prompt Builder"]
        LLMProvider["Groq LPU Provider (Llama-3/4 Series)"]
    end

    subgraph Storage ["Persistent State (PostgreSQL 16 + pgvector)"]
        Relational["Users, Repositories, Jobs, Snapshots"]
        ASTData["Files, Symbols, Dependency Edges"]
        VectorData["Code Chunks & pgvector HNSW Embeddings"]
        DocsData["Repository Documents & PR Reviews"]
    end

    UI -->|HTTP / SSE| Router
    GraphUI -->|Graph API| Router
    ChatUI -->|Chat Stream| Router
    SearchUI -->|Search API| Router

    Router --> AuthMiddleware
    AuthMiddleware --> JobManager
    
    JobManager --> Cloner
    Cloner --> Parser
    Parser --> Extractor
    Extractor --> Chunker
    Chunker --> VectorData
    Extractor --> ASTData
    
    Router --> Planner
    Planner --> Executor
    Executor --> Tools
    Tools --> ASTData
    Tools --> VectorData
    Executor --> ContextBuilder
    ContextBuilder --> LLMProvider
    LLMProvider --> Router

    Router --> Relational
```

---

## 4. AI & Multi-Agent Architecture

RepoWeave does not rely on static prompt stuffing. Instead, it implements an **autonomous agentic RAG loop** designed to retrieve exact code context dynamically.

```
User Query
    │
    ▼
┌───────────────────────────┐
│     LLM Tool Planner      │ ◄── Evaluates question against Tool Registry
└─────────────┬─────────────┘
              │ Outputs JSON Execution Plan
              ▼
┌───────────────────────────┐
│   Tool Execution Engine   │
├───────────────────────────┤
│ • search_tool             │ ──► Hybrid BM25 full-text + pgvector dense search
│ • graph_tool              │ ──► Blast radius, callers/callees, shortest paths
│ • branch_tool             │ ──► Branch diffs and snapshot metadata
│ • metadata_tool           │ ──► File counts, language distributions
└─────────────┬─────────────┘
              │ Structured Observations & Raw Chunks
              ▼
┌───────────────────────────┐
│      Context Builder      │ ──► Deduplicates, formats citations, enforces char budget
└─────────────┬─────────────┘
              │ Grounded Prompt Context
              ▼
┌───────────────────────────┐
│     LLM Generation        │ ──► Groq LPU API (Streaming SSE or JSON Response)
└─────────────┬─────────────┘
              ▼
   Verified Output + Source Citations + Execution Timing
```

### LLM Integration & Providers

- **Inference Acceleration:** Powered by **Groq High-Throughput LPU Inference**, delivering sub-second reasoning latency across complex code analysis prompts.
- **Model Fallback Chain:** Configured with primary model `groq/compound-mini` and automatic resilience fallback chains to `groq/compound` and `meta-llama/llama-4-scout-17b-16e-instruct`.
- **Rate-Limiting Protection:** Built-in `TokenBucketRateLimiter` ensures compliant traffic smoothing across provider rate quotas.
- **Embeddings:** Dual-pipeline architecture:
  - **Local In-Memory:** `BAAI/bge-small-en-v1.5` via FastEmbed for local zero-cost processing.
  - **Cloud Embedding:** Google Gemini Embeddings (`gemini-embedding-001`) via Google AI API for cloud environments.

---

## 5. Technology Stack

### Backend
- **Framework:** FastAPI 0.138+ (ASGI asynchronous runtime)
- **Language:** Python 3.11
- **Database ORM:** SQLAlchemy 2.0 (Asyncio)
- **Database Driver:** asyncpg 0.31+
- **Schema Validation:** Pydantic v2 & Pydantic Settings
- **Migrations:** Alembic 1.18+
- **Security:** Passlib (bcrypt), python-jose (JWT), HTTP-only cookies
- **AST Parsing:** Tree-sitter 0.24+ (`tree-sitter-python`, `tree-sitter-javascript`, `tree-sitter-go`, `tree-sitter-c`, `tree-sitter-cpp`)
- **Git Operations:** GitPython 3.1+
- **Package Manager:** [uv](https://docs.astral.sh/uv/) (Astral)

### Frontend
- **Framework:** Next.js 16 (App Router)
- **Library:** React 19
- **Language:** TypeScript 5
- **Styling:** Tailwind CSS v4, tw-animate-css
- **Components:** shadcn/ui primitives, Lucide React icons
- **Graph Visualization:** `@xyflow/react` (React Flow 12), `@dagrejs/dagre`
- **Client State & Caching:** TanStack React Query v5
- **Markdown Rendering:** react-markdown, remark-gfm

### Storage & Data
- **Primary Database:** PostgreSQL 16
- **Vector Index:** `pgvector` extension (HNSW and IVFFlat index support)
- **Text Search:** PostgreSQL Full-Text Search (`tsvector`, GIN indexes)

### Infrastructure & Operations
- **Containerization:** Docker multi-stage builds, Docker Compose
- **Cloud Blueprint:** Render Blueprint specification (`infrastructure/render.yaml`)
- **Continuous Integration:** GitHub Actions (`backend-ci.yml`, `frontend-ci.yml`)

---

## 6. Project Structure

```text
repo-weave/
├── .github/
│   └── workflows/
│       ├── backend-ci.yml         # Python lint, test, migration pipeline
│       └── frontend-ci.yml        # Next.js lint, vitest, build pipeline
├── backend/
│   ├── alembic/                   # Database migration versions (001-011)
│   ├── app/
│   │   ├── api/
│   │   │   ├── deps.py            # Authentication and session dependencies
│   │   │   └── v1/
│   │   │       ├── endpoints/     # admin, auth, health, jobs, repositories, version
│   │   │       └── router.py      # Route configuration
│   │   ├── core/                  # config, database, security, logging, cookies
│   │   ├── middleware/            # request_logging middleware
│   │   ├── models/                # SQLAlchemy models (User, Repository, Chunk, Symbol, etc.)
│   │   ├── repositories/          # Data access layer
│   │   ├── schemas/               # Pydantic request and response schemas
│   │   ├── services/
│   │   │   ├── ai/                # AIEngine, context builder, prompt builder, tools
│   │   │   ├── analysis/          # Tree-sitter parser, cloner, scanner, AST extractor
│   │   │   ├── documentation/     # Automated documentation generator
│   │   │   ├── graph/             # Structure graph, blast radius, shortest path
│   │   │   ├── indexing/          # Semantic chunking and embedding backends
│   │   │   ├── pr_review/         # Pull request review engine
│   │   │   ├── auth_service.py    # User authentication logic
│   │   │   └── search_service.py  # Hybrid full-text + vector search service
│   │   └── utils/                 # File chunker, filter, syntax highlighter
│   ├── tests/                     # 55+ comprehensive backend pytest suites
│   ├── Dockerfile                 # Multi-stage Python 3.11 + uv Dockerfile
│   └── pyproject.toml             # Hatchling build & dependency manifest
├── frontend/
│   ├── public/                    # Static assets, branding icons
│   ├── src/
│   │   ├── app/                   # Next.js App Router (dashboard, repositories, admin)
│   │   ├── components/            # UI components, React Flow graph, chat, search drawer
│   │   ├── contexts/              # Auth context and session management
│   │   ├── hooks/                 # React query hooks (chat, search, jobs, repositories)
│   │   ├── lib/                   # API client, graph layout, utility functions
│   │   ├── services/              # Client API call services
│   │   └── types/                 # TypeScript interface definitions
│   ├── Dockerfile                 # Next.js standalone runner Dockerfile
│   └── package.json               # Frontend dependencies and script definitions
├── docs/
│   ├── api.md                     # REST API reference and endpoints
│   ├── architecture.md            # In-depth architectural blueprint
│   ├── installation.md            # Comprehensive installation and setup guide
│   └── roadmap.md                 # Product milestone roadmap
├── infrastructure/
│   └── render.yaml                # Render Blueprint deployment definition
├── docker-compose.yml             # Local multi-service development compose
├── CHANGELOG.md                   # Platform release history
├── CONTRIBUTING.md                # Contribution guidelines and development workflow
├── LICENSE                        # MIT License
└── SECURITY.md                    # Security policy and vulnerability disclosure
```

---

## 7. Installation and Setup

### Prerequisites

- **Python:** 3.11 (`>=3.11,<3.12`)
- **Package Manager:** `uv` (`pip install uv` or install via [Astral](https://docs.astral.sh/uv/))
- **Node.js:** 20+ (`LTS`) and `npm`
- **Database:** PostgreSQL 16 with the `pgvector` extension enabled
- **API Keys:** Groq API key (required for AI capabilities), Google AI API key (optional, for Gemini embeddings), GitHub Token (optional, for higher rate limits)

---

### Method A: Local Development Setup

#### 1. Clone the Repository
```bash
git clone https://github.com/yousefkhaled2005/repo-weave.git
cd repo-weave
```

#### 2. Configure Backend Environment
```bash
cd backend
cp .env.example .env
```

Edit `backend/.env` with your settings:
```ini
ENV=development
DATABASE_URL=postgresql+asyncpg://postgres:postgres@localhost:5432/repoweave
SECRET_KEY=your-development-secret-key-at-least-32-chars
GROQ_API_KEY=gsk_your_groq_api_key_here
GITHUB_TOKEN=ghp_your_github_token_here
EMBEDDING_PROVIDER=local
```

#### 3. Install Dependencies & Run Database Migrations
```bash
# Sync all dependencies including dev tools via uv
uv sync --extra dev

# Run Alembic migrations to create tables and pgvector indexes
uv run alembic upgrade head

# Launch the FastAPI backend server
uv run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```
*The backend API documentation is now live at `http://localhost:8000/docs`.*

#### 4. Configure & Start Frontend
```bash
cd ../frontend
cp .env.example .env.local
npm install
npm run dev
```
*The web console is accessible at `http://localhost:3000`.*

---

### Method B: Docker Compose Setup

Run the full platform (backend, frontend, and database network) with a single command:

```bash
# Ensure backend/.env and frontend/.env.local are configured
docker compose up -d --build
```

- Web Console: `http://localhost:3000`
- API Gateway: `http://localhost:8000`
- Interactive API Docs: `http://localhost:8000/docs`

---

## 8. Configuration Reference

| Variable | Default | Purpose |
|:---|:---|:---|
| `ENV` | `development` | Environment mode (`development` or `production`). |
| `DATABASE_URL` | `postgresql+asyncpg://localhost/postgres` | PostgreSQL connection string using `asyncpg`. |
| `SECRET_KEY` | `change-me-in-production` | Secret string for signing JWT tokens (>=32 chars required in prod). |
| `GROQ_API_KEY` | *None* | Groq API Key for LLM reasoning and agent tool planning. |
| `GOOGLE_API_KEY` | *None* | Required if `EMBEDDING_PROVIDER=google`. |
| `EMBEDDING_PROVIDER` | `local` | Embedding backend: `local` (FastEmbed) or `google` (Gemini API). |
| `EMBEDDING_MODEL_NAME` | `BAAI/bge-small-en-v1.5` | Embedding model identifier. |
| `EMBEDDING_DIMENSION` | `384` | Embedding vector dimension (384 for BGE-small, 768 for Gemini). |
| `GITHUB_TOKEN` | *None* | Personal Access Token to prevent GitHub API rate-limiting during clones. |
| `CORS_ORIGINS` | `["http://localhost:3000"]` | Allowed CORS origins (JSON array or comma-separated strings). |
| `LLM_MODEL` | `groq/compound-mini` | Primary LLM model for generation. |
| `LLM_MODEL_FALLBACKS` | `["groq/compound", "..."]` | Ordered fallback models if primary model rate-limits. |
| `TOOL_MAX_STEPS` | `4` | Maximum tool execution steps permitted per agent plan. |
| `NEXT_PUBLIC_API_URL` | `http://localhost:8000` | Backend API URL accessed by frontend browser client. |
| `NEXT_PUBLIC_API_PROXY` | `false` | Enable same-origin Vercel `/api/*` proxy rewrite. |

---

## 9. API Specification

RepoWeave exposes clean, standardized REST APIs grouped under `/api/v1`:

- **Authentication (`/api/v1/auth`)**:
  - `POST /register` — Register a new user workspace account.
  - `POST /login` — Authenticate and receive HTTP-only JWT session cookie.
  - `GET /me` — Retrieve active user session profile.
  - `POST /logout` — Terminate session and clear cookie.
- **Repository Operations (`/api/v1/repositories`)**:
  - `GET /` — List analyzed repositories in workspace.
  - `POST /analyze` — Submit repository URL for asynchronous ingestion.
  - `GET /{id}` — Fetch repository summary and status.
  - `GET /{id}/branches` — List analyzed Git branches.
  - `GET /{id}/graph` — Retrieve interactive dependency nodes and edges.
  - `GET /{id}/graph/blast-radius` — Calculate transitive change impact for a symbol/file.
  - `GET /{id}/graph/path` — Find shortest dependency path between files.
  - `GET /{id}/search` — Execute hybrid keyword + vector semantic search.
  - `POST /{id}/chat` — Query repository AI agent with citation verification.
  - `GET /{id}/pull-requests` — View synchronized pull requests.
  - `GET /{id}/pull-requests/{pr_id}/review` — Retrieve AI-generated code review.
  - `GET /{id}/documentation/{doc_type}` — Retrieve generated architectural documentation.
- **Jobs & Tasks (`/api/v1/jobs`)**:
  - `GET /{id}` — Poll background job status and real-time execution event logs.
- **System Health (`/health`)**:
  - `GET /live` — Kubernetes/Container liveness probe.
  - `GET /ready` — Readiness probe verifying database connectivity.

For detailed request and response payload schemas, refer to [docs/api.md](docs/api.md).

---

## 10. Testing & Verification

RepoWeave enforces high testing standards across both backend and frontend codebases:

### Backend Tests
```bash
cd backend

# Execute complete pytest suite
uv run pytest

# Run with coverage report
uv run pytest --cov=app

# Run code style & format validation
uv run black --check app tests
uv run isort --check-only app tests
uv run flake8 app tests
```

### Frontend Tests
```bash
cd frontend

# Run Vitest test suites
npm test

# Run ESLint validation
npm run lint

# Test production build compilation
npm run build
```

---

## 11. Security & Operational Hardening

- **Cookie-Based Authentication:** Tokens are transmitted via secure, HTTP-only cookies with `SameSite=Lax` protection, preventing token theft via Cross-Site Scripting (XSS).
- **Production Secret Validation:** Startup checks enforce that `SECRET_KEY` cannot be default insecure values and must exceed 32 characters in production mode.
- **Credential Protection:** API keys (`GROQ_API_KEY`, `GOOGLE_API_KEY`, `GITHUB_TOKEN`) are injected strictly through environment variables and never logged or exposed to the client.
- **Sandbox Repository Access:** Cloned repositories are isolated in configurable temporary storage directories (`CLONE_BASE_DIR`) and cleaned up following analysis jobs.

---

## 12. Product Roadmap

For our comprehensive milestone schedule and feature specifications, see [docs/roadmap.md](docs/roadmap.md).

- **Current Release (v1.2.0 - Completed):**
  - [x] Multi-language CST parsing with Tree-sitter (Python, JS, TS, Go, C, C++).
  - [x] Hybrid BM25 full-text + pgvector dense embedding retrieval.
  - [x] Autonomous multi-agent tool planning (Search, Graph, Branch, Metadata tools).
  - [x] Interactive dependency blast-radius and shortest-path calculation.
  - [x] Automated PR review generation based on diff hunks and dependency impact.
  - [x] Dynamic documentation generator for Architecture, Overview, and Onboarding.
- **In-Progress:**
  - [ ] Multi-branch concurrent index synchronization.
  - [ ] Cross-encoder re-ranking pass in worker pipeline.
- **Planned:**
  - [ ] Direct provider adapters for Anthropic Claude and OpenAI APIs.
  - [ ] Language grammar expansion to Rust, Java, and C#.
  - [ ] GitHub App webhook auto-triggering on Pull Request creation.
  - [ ] Multi-repository workspace federation.

---

## 13. Contributing

We welcome community contributions from developers, researchers, and architects. Please review our [CONTRIBUTING.md](CONTRIBUTING.md) guide for branch naming conventions, development setup instructions, and pull request procedures.

---

## 14. License and Attribution

RepoWeave is open-source software licensed under the [MIT License](LICENSE).

### Historical Provenance & Credits
- Originally created as GitSight by the open-source engineering community.
- Rebranded and actively maintained as **RepoWeave** under the leadership of [**Yousef Khaled Gouda**](https://github.com/yousefkhaled2005) with automated collaborative assistance from **Claude Code** and **GitHub Actions**.
- Copyright (c) 2026 GitSight Contributors / RepoWeave Contributors.

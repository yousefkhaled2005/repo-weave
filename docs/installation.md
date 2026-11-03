# RepoWeave Installation & Deployment Guide

This guide provides complete instructions for setting up RepoWeave across local development, Docker Compose, and production cloud environments.

---

## 1. System Requirements

### Hardware Prerequisites
- **CPU:** 2+ cores recommended.
- **RAM:** Minimum 2 GB (4 GB recommended if running local FastEmbed models).
- **Disk:** 5 GB free disk space for cloned repository workspaces and database tables.

### Software Prerequisites
- **Python:** Version `3.11.x`
- **Package Manager (Backend):** [uv](https://docs.astral.sh/uv/) (v0.5.0+)
- **Node.js:** Version `20.x` or `22.x` (LTS)
- **Package Manager (Frontend):** `npm` (v10+)
- **Database:** PostgreSQL 15+ with the [pgvector](https://github.com/pgvector/pgvector) extension installed.
- **API Keys:**
  - [Groq API Key](https://console.groq.com) (Required for AI chat & agent workflows)
  - GitHub Personal Access Token (Optional but recommended for public API rate limits)
  - Google Gemini API Key (Optional; required only if using `EMBEDDING_PROVIDER=google`)

---

## 2. Local Development Setup

### 2.1 Clone the Repository

```bash
git clone https://github.com/yousefkhaled2005/repo-weave.git
cd repo-weave
```

### 2.2 Database Initialization

Ensure PostgreSQL is running and has the `vector` extension created:

```sql
CREATE DATABASE repoweave;
\c repoweave;
CREATE EXTENSION IF NOT EXISTS vector;
```

### 2.3 Backend Setup

1. Navigate to the backend directory:
   ```bash
   cd backend
   ```

2. Create your environment configuration:
   ```bash
   cp .env.example .env
   ```

3. Configure required variables in `backend/.env`:
   ```ini
   ENV=development
   DATABASE_URL=postgresql+asyncpg://postgres:postgres@localhost:5432/repoweave
   SECRET_KEY=generate-a-secure-random-32-char-key-here
   GROQ_API_KEY=gsk_your_groq_api_key_here
   GITHUB_TOKEN=ghp_optional_github_token_here
   EMBEDDING_PROVIDER=local
   EMBEDDING_MODEL_NAME=BAAI/bge-small-en-v1.5
   EMBEDDING_DIMENSION=384
   ```

4. Install dependencies using `uv`:
   ```bash
   uv sync --extra dev
   ```

5. Run database migrations:
   ```bash
   uv run alembic upgrade head
   ```

6. Start the FastAPI development server:
   ```bash
   uv run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
   ```

   The backend API will be available at `http://localhost:8000` with interactive OpenAPI docs at `http://localhost:8000/docs`.

### 2.4 Frontend Setup

1. Open a new terminal and navigate to the frontend directory:
   ```bash
   cd frontend
   ```

2. Create your local environment file:
   ```bash
   cp .env.example .env.local
   ```

3. Ensure `frontend/.env.local` points to your backend:
   ```ini
   NEXT_PUBLIC_API_URL=http://localhost:8000
   NEXT_PUBLIC_API_PROXY=false
   ```

4. Install dependencies:
   ```bash
   npm install
   ```

5. Start the Next.js development server:
   ```bash
   npm run dev
   ```

   The web interface will be accessible at `http://localhost:3000`.

---

## 3. Docker Compose Deployment

RepoWeave includes a multi-container Docker Compose configuration for quick local evaluation.

1. Ensure Docker Engine and Docker Compose v2 are installed and running.

2. Populate required environment variables in a root `.env` or set them in your shell:
   ```bash
   export GROQ_API_KEY=gsk_your_groq_api_key
   export SECRET_KEY=your-32-char-secret-key
   ```

3. Build and launch all services:
   ```bash
   docker compose up --build -d
   ```

4. Verify service health:
   ```bash
   docker compose ps
   ```

5. Access the application:
   - Frontend: `http://localhost:3000`
   - Backend API: `http://localhost:8000`
   - API Docs: `http://localhost:8000/docs`

---

## 4. Production Cloud Deployment (Render + Supabase + Vercel)

For zero-server-maintenance hosting, RepoWeave is pre-configured for:
- **Database:** Supabase PostgreSQL with `pgvector`
- **Backend:** Render Web Service (Docker runtime)
- **Frontend:** Vercel Edge / Serverless

### 4.1 Supabase Setup
1. Create a new Supabase project.
2. In the SQL Editor, enable `pgvector`:
   ```sql
   CREATE EXTENSION IF NOT EXISTS vector;
   ```
3. Copy the transaction pooler connection string (port 6543) or direct connection string. Ensure the protocol is formatted as `postgresql+asyncpg://`.

### 4.2 Render Backend Deployment
1. Connect your GitHub repository to Render.
2. Use the included `infrastructure/render.yaml` blueprint or create a Web Service:
   - **Environment:** Docker
   - **Docker Context:** `./backend`
   - **DockerfilePath:** `./backend/Dockerfile`
   - **Health Check Path:** `/health/ready`
   - **Pre-Deploy Command:** `uv run alembic upgrade head`
3. Configure Environment Variables:
   - `ENV`: `production`
   - `DATABASE_URL`: `postgresql+asyncpg://postgres.[ref]:[pw]@aws-0-[region].pooler.supabase.com:6543/postgres`
   - `SECRET_KEY`: `[generated-32-char-random-key]`
   - `GROQ_API_KEY`: `[your-groq-api-key]`
   - `GITHUB_TOKEN`: `[your-github-token]`
   - `EMBEDDING_PROVIDER`: `google`
   - `GOOGLE_API_KEY`: `[your-google-ai-api-key]`
   - `EMBEDDING_MODEL_NAME`: `gemini-embedding-001`
   - `EMBEDDING_DIMENSION`: `384`
   - `CORS_ORIGINS`: `["https://your-repoweave-app.vercel.app"]`

### 4.3 Vercel Frontend Deployment
1. Import your GitHub repository to Vercel.
2. Set **Root Directory** to `frontend`.
3. Configure Environment Variables:
   - `NEXT_PUBLIC_API_URL`: Your Render backend URL (e.g., `https://repoweave-api.onrender.com`)
   - `NEXT_PUBLIC_API_PROXY`: `true` (enables Next.js API route rewrites to proxy requests and preserve cookies across domains)
4. Deploy the application.

---

## 5. Verification & Testing

Verify that your installation is fully functional:

```bash
# 1. Check Backend Health
curl -i http://localhost:8000/health/ready
# Expected: HTTP/1.1 200 OK {"status": "ready"}

# 2. Run Backend Test Suite
cd backend
uv run pytest

# 3. Run Frontend Test Suite
cd ../frontend
npm test
```

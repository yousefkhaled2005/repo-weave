# RepoWeave REST API Reference

**Base URL:** `http://localhost:8000/api/v1` (Local) or `https://repoweave-api.onrender.com/api/v1` (Production)  
**OpenAPI Documentation:** `/docs` (Swagger UI) or `/redoc` (ReDoc)  
**Authentication Scheme:** Cookie-based JWT (`access_token` HttpOnly cookie) or `Authorization: Bearer <token>` header.

---

## 1. Authentication Endpoints

### 1.1 User Registration
- **Method & Path:** `POST /auth/register`
- **Status Code:** `201 Created`
- **Request Body (`application/json`):**
  ```json
  {
    "username": "developer",
    "email": "dev@example.com",
    "password": "StrongPassword123!"
  }
  ```
- **Response (`201 Created`):**
  ```json
  {
    "id": "usr_9f8d7c6b-5a4e-3d2c-1b0a-9f8e7d6c5b4a",
    "username": "developer",
    "email": "dev@example.com",
    "role": "user",
    "created_at": "2026-11-03T18:00:00Z"
  }
  ```

### 1.2 User Login
- **Method & Path:** `POST /auth/login`
- **Status Code:** `200 OK`
- **Description:** Verifies credentials and sets the `access_token` cookie.
- **Request Body (`application/json`):**
  ```json
  {
    "email": "dev@example.com",
    "password": "StrongPassword123!"
  }
  ```
- **Response (`200 OK`):** Returns authenticated `UserResponse` model and sets `Set-Cookie` header.

### 1.3 Current User Profile
- **Method & Path:** `GET /auth/me`
- **Status Code:** `200 OK`
- **Auth Required:** Yes
- **Response (`200 OK`):** Current authenticated user session details.

### 1.4 User Logout
- **Method & Path:** `POST /auth/logout`
- **Status Code:** `200 OK`
- **Description:** Clears the `access_token` cookie.

---

## 2. Repository Management & Ingestion

### 2.1 Ingest & Analyze Repository (Async Background Job)
- **Method & Path:** `POST /repositories/analyze`
- **Status Code:** `202 Accepted`
- **Auth Required:** Yes
- **Request Body (`application/json`):**
  ```json
  {
    "github_url": "https://github.com/fastapi/fastapi"
  }
  ```
- **Response (`202 Accepted`):**
  ```json
  {
    "repository_id": "rep_1a2b3c4d-5e6f-7a8b-9c0d-1e2f3a4b5c6d",
    "job_id": "job_9z8y7x6w-5v4u-3t2s-1r0q-9p8o7n6m5l4k",
    "status": "queued",
    "message": "Repository analysis queued in background"
  }
  ```

### 2.2 List User Repositories
- **Method & Path:** `GET /repositories`
- **Status Code:** `200 OK`
- **Auth Required:** Yes
- **Response (`200 OK`):** List of ingested repositories with file counts, ingestion statuses, and star counts.

### 2.3 Get Repository Summary
- **Method & Path:** `GET /repositories/{repository_id}`
- **Status Code:** `200 OK`
- **Auth Required:** Yes

### 2.4 Delete Repository
- **Method & Path:** `DELETE /repositories/{repository_id}`
- **Status Code:** `204 No Content`
- **Auth Required:** Yes

---

## 3. Code Intelligence & Search

### 3.1 Hybrid Code Search
- **Method & Path:** `GET /repositories/{repository_id}/search`
- **Status Code:** `200 OK`
- **Auth Required:** Yes
- **Query Parameters:**
  - `q` (string, required): Search query or natural language concept.
  - `limit` (integer, optional, default: `20`, max: `100`): Max results to return.
  - `keyword_weight` (float, optional, default: `0.4`): Weight for lexical match.
  - `semantic_weight` (float, optional, default: `0.6`): Weight for vector similarity.
- **Response (`200 OK`):**
  ```json
  {
    "query": "jwt authentication token creation",
    "total_results": 12,
    "results": [
      {
        "chunk_id": "chk_4a5b6c7d",
        "file_path": "app/core/security.py",
        "start_line": 24,
        "end_line": 52,
        "symbol_name": "create_access_token",
        "symbol_type": "function",
        "score": 0.892,
        "content": "def create_access_token(subject: str | Any, expires_delta: timedelta = None) -> str:..."
      }
    ]
  }
  ```

### 3.2 List Code Chunks
- **Method & Path:** `GET /repositories/{repository_id}/chunks`
- **Query Parameters:** `page` (default: 1), `size` (default: 50), `file_path` (optional).

### 3.3 Inspect Single Code Chunk
- **Method & Path:** `GET /repositories/{repository_id}/chunks/{chunk_id}`

---

## 4. Dependency Graph & Structural Analysis

### 4.1 Get Full Dependency Graph
- **Method & Path:** `GET /repositories/{repository_id}/graph`
- **Status Code:** `200 OK`
- **Description:** Returns nodes (files/modules) and directed edges (import dependencies) formatted for visualization.

### 4.2 Dependency Blast Radius Calculation
- **Method & Path:** `GET /repositories/{repository_id}/graph/blast-radius`
- **Query Parameters:**
  - `target_file` (string, required): Relative file path to analyze.
  - `max_depth` (integer, optional, default: `3`): Traversal depth.
- **Response (`200 OK`):** Lists all upstream and downstream affected files, depth levels, and impacted symbols.

### 4.3 Shortest Dependency Path
- **Method & Path:** `GET /repositories/{repository_id}/graph/path`
- **Query Parameters:**
  - `source_file` (string, required): Origin module.
  - `target_file` (string, required): Destination module.
- **Response (`200 OK`):** Ordered array of intermediary dependency hops.

---

## 5. AI Engineering Workflows & Chat

### 5.1 Context-Aware Agent Chat
- **Method & Path:** `POST /repositories/{repository_id}/chat`
- **Status Code:** `200 OK`
- **Auth Required:** Yes
- **Request Body (`application/json`):**
  ```json
  {
    "message": "Where is the authentication middleware registered and how are CORS origins validated?",
    "thread_id": "thr_optional_existing_thread_id"
  }
  ```
- **Response (`200 OK`):**
  ```json
  {
    "thread_id": "thr_6e7d8c9b-0a1b-2c3d-4e5f-6a7b8c9d0e1f",
    "reply": "The authentication middleware is registered in `app/main.py`...",
    "tools_called": [
      {
        "tool": "search_code",
        "arguments": {"query": "add_middleware CORS"},
        "result_count": 2
      },
      {
        "tool": "read_file",
        "arguments": {"file_path": "app/main.py", "start_line": 1, "end_line": 50}
      }
    ],
    "model_used": "groq/compound-mini"
  }
  ```

---

## 6. Pull Request Analysis & Documentation

### 6.1 List Repository Pull Requests
- **Method & Path:** `GET /repositories/{repository_id}/pull-requests`

### 6.2 Trigger Autonomous Pull Request Review
- **Method & Path:** `POST /repositories/{repository_id}/pull-requests/{number}/review`
- **Status Code:** `200 OK`
- **Description:** Analyzes PR git diffs, traverses affected dependencies via the blast radius engine, runs the Groq agent to detect architectural bugs or regressions, and returns structured feedback.

### 6.3 Generate Repository Documentation
- **Method & Path:** `POST /repositories/{repository_id}/documentation/{document_type}/generate`
- **Supported `document_type` values:** `architecture`, `overview`, `api`, `onboarding`
- **Response (`200 OK`):** Generates and caches structured Markdown documentation synthesized from the AST symbols and dependency graph.

---

## 7. System Health & Jobs

### 7.1 Liveness Probe
- **Method & Path:** `GET /health`
- **Response (`200 OK`):** `{"status": "ok"}`

### 7.2 Readiness Probe
- **Method & Path:** `GET /health/ready`
- **Response (`200 OK`):** Validates database connection pool and vector extension readiness.

### 7.3 Background Job Status
- **Method & Path:** `GET /jobs/{job_id}`
- **Response (`200 OK`):** Returns job state (`queued`, `processing`, `completed`, `failed`), progress percentage, and error messages if failed.

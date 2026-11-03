# RepoWeave System Architecture

**Product:** RepoWeave  
**Tagline:** Understand. Connect. Engineer.  
**Category:** AI-Powered Repository Intelligence Platform  

---

## 1. Executive Architecture Summary

RepoWeave is an autonomous, repository-level software engineering intelligence platform. It ingests public Git repositories, extracts AST-level code symbols and import dependency structures via Tree-sitter, computes dense vector embeddings, and exposes an AI-driven multi-step investigation agent powered by Groq LPUs.

The system is decoupled into two primary tiers:
1. **Frontend Client Tier:** Next.js 16 (App Router), React 19, Tailwind CSS v4, `@xyflow/react` (React Flow 12), and TanStack Query v5.
2. **Backend Engine Tier:** FastAPI 0.138, SQLAlchemy 2.0 Async ORM, PostgreSQL with `pgvector`, Tree-sitter parsers, FastEmbed / Google Gemini embeddings, and Groq LLM orchestration.

---

## 2. End-to-End System Topology

```mermaid
flowchart TB
    subgraph Client ["Client Tier (Next.js 16 + React 19)"]
        UI["Web UI & Dashboard"]
        RF["Interactive Dependency Graph (@xyflow/react)"]
        Chat["Context-Aware Chat & Reasoning Stream"]
        SearchUI["Hybrid Code Search Interface"]
        DocViewer["Repository Documentation Viewer"]
    end

    subgraph Gateway ["API & Edge Gateway"]
        Proxy["Next.js API Rewrite Proxy (/api/*)"]
        CORS["FastAPI CORS & Auth Middleware"]
        JWT["HttpOnly Cookie JWT Authenticator"]
    end

    subgraph Backend ["Backend Core (FastAPI + Async Python 3.11)"]
        API["REST API Router (/api/v1)"]
        Ingest["Repository Ingestion Worker"]
        Parser["Tree-sitter AST & Import Analyzer"]
        Embedder["Embedding Engine (FastEmbed / Gemini API)"]
        SearchEng["Hybrid Search Engine (RRF / Weighted Score)"]
        Agent["ReAct Multi-Step Agent Orchestrator"]
    end

    subgraph Data ["Persistence & Vector Layer"]
        PG[(PostgreSQL + pgvector)]
        Disk["Ephemeral Clone Workspace (/tmp)"]
    end

    subgraph External ["External Services"]
        GH["GitHub REST API (v3)"]
        Groq["Groq Cloud LLM (Compound-Mini / Llama-4)"]
        GoogleAPI["Google Gemini Embedding API"]
    end

    %% Client to Gateway
    UI -->|HTTP / JSON| Proxy
    RF -->|HTTP / JSON| Proxy
    Chat -->|HTTP / JSON| Proxy
    SearchUI -->|HTTP / JSON| Proxy
    DocViewer -->|HTTP / JSON| Proxy
    Proxy -->|Reverse Proxy| CORS
    CORS --> JWT
    JWT --> API

    %% Backend processing
    API --> Ingest
    API --> SearchEng
    API --> Agent

    Ingest -->|Shallow Git Clone| GH
    Ingest -->|Disk Write| Disk
    Ingest -->|File Traversal| Parser
    Parser -->|AST Parsing (Py, JS, TS, Go, C, C++)| Parser
    Parser -->|Text Chunks| Embedder
    
    Embedder -.->|Cloud Option| GoogleAPI
    Embedder -->|Dense Vectors (384-dim)| PG
    Parser -->|Files, Chunks, Symbols, Graph Nodes| PG

    SearchEng -->|Cosine Vector Similarity| PG
    SearchEng -->|SQL ILIKE / Trigram Text Match| PG

    Agent -->|Repository Tool Invocations| SearchEng
    Agent -->|Graph Navigation| PG
    Agent -->|Inference Prompts| Groq
```

---

## 3. Core Subsystems

### 3.1 Ingestion & Parsing Pipeline

When a user submits a public GitHub repository:
1. **GitHub Metadata Resolution:** Fetches repository metadata, commits, branches, and active Pull Requests via GitHub REST API v3 using an optional `GITHUB_TOKEN`.
2. **Controlled Git Clone:** Performs a shallow clone (`--depth 1`) into an isolated working directory under `/tmp`.
3. **Multi-Language Tree-sitter Parsing:**
   - Detects file extension and routes files to Tree-sitter language grammars:
     - Python (`tree-sitter-python`)
     - JavaScript (`tree-sitter-javascript`)
     - TypeScript & TSX (`tree-sitter-typescript`)
     - Go (`tree-sitter-go`)
     - C & C++ (`tree-sitter-c`, `tree-sitter-cpp`)
   - Traverses Concrete Syntax Trees (CST) to extract functions, classes, methods, signatures, docstrings, line spans, and import statements.
4. **Dependency Graph Construction:**
   - Extracts source and target imports.
   - Resolves internal relative and module paths against ingested repository files.
   - Populates `dependency_nodes` and `dependency_edges` in PostgreSQL.

### 3.2 Embedding & Vector Storage Engine

Code chunks (bounded to configurable line thresholds, default max 120 lines) are converted into dense vector representations:
- **Local Engine (Default):** ONNX-runtime based `fastembed` using `BAAI/bge-small-en-v1.5` producing 384-dimensional vectors. Requires zero external network calls for vector generation.
- **Hosted Engine (Cloud Production):** `gemini-embedding-001` via Google GenAI API, configured to match the 384-dimensional `pgvector` column constraint, eliminating RAM overhead on constrained hosting tiers (e.g., Render free/starter tiers).
- **Storage:** Persisted directly in PostgreSQL using the `vector(384)` type with HNSW / IVFFlat indexed similarity search.

### 3.3 Hybrid Search Engine

RepoWeave combines semantic and lexical search strategies:
- **Vector Search:** Computes cosine distance (`<=>` operator) against indexed code chunk embeddings.
- **Keyword Search:** Case-insensitive substring matching (`ILIKE`) across code chunk content and file paths.
- **Combined Scoring:** Calculates normalized scores:
  $$\text{Score} = w_{\text{semantic}} \times S_{\text{semantic}} + w_{\text{keyword}} \times S_{\text{keyword}}$$
  (Default weights: $w_{\text{semantic}} = 0.6$, $w_{\text{keyword}} = 0.4$).

### 3.4 Multi-Step AI Agent & Tool Execution

RepoWeave employs an autonomous ReAct-style agent orchestration loop powered by Groq LPUs (`groq/compound-mini` with fallback to `groq/compound` and `meta-llama/llama-4-scout-17b-16e-instruct`):
- **Planning Step:** Evaluates user prompt, repository structure, and available tools.
- **Tool Execution:**
  - `search_code`: Executes hybrid search over indexed chunks.
  - `read_file`: Fetches raw file content or bounded line ranges from storage.
  - `get_dependencies`: Queries the dependency graph for inbound/outbound edges.
  - `list_files`: Enumerates directory structures and file trees.
- **Synthesis:** Synthesizes multi-file findings into structured Markdown responses with precise file references, syntax-highlighted code, and architectural reasoning.

---

## 4. Database Schema Overview

The relational and vector schema comprises 11 versioned Alembic migrations:

| Table | Primary Purpose |
|-------|-----------------|
| `users` | User credentials, bcrypt password hashes, and roles (`admin`, `user`). |
| `repositories` | GitHub repo metadata, clone status, branch, ingestion state, and star count. |
| `repository_branches` | Branch metadata and commit tracking. |
| `repository_files` | File path, size, language, extension, and content checksum. |
| `code_chunks` | Bounded code segments, AST symbol metadata, line numbers, and 384-dim vector embedding. |
| `dependency_nodes` | Dependency graph vertices (files, internal modules, external packages). |
| `dependency_edges` | Dependency graph edges (imports, calls, references) with relation types. |
| `chat_threads` | Conversation sessions linked to a user and repository. |
| `chat_messages` | ReAct messages, tool call traces, and model completions. |
| `pull_requests` | Cached PR metadata, diffs, analysis results, and review comments. |
| `repository_documents` | AI-generated architecture overviews, onboarding guides, and API specs. |

---

## 5. Security & Isolation Model

- **Session Security:** JWT stored in `HttpOnly`, `SameSite=Lax` cookies; protection against XSS token exfiltration.
- **Workspace Isolation:** Ephemeral git checkouts operate in temporary OS directories with sandboxed file permissions and automatic teardown.
- **Input Validation:** Strict Pydantic models validate all incoming payloads; SQL injection prevention via SQLAlchemy parameterized queries.

# RepoWeave Strategic Roadmap

This document outlines the architectural roadmap for RepoWeave, distinguishing completed milestones from ongoing initiatives and future architectural plans.

---

## 1. Completed Milestones (v1.0.0 – v1.2.0)

### Ingestion & Parsing Engine
- [x] Shallow Git repository ingestion via GitHub REST API v3.
- [x] Tree-sitter AST parser integrations:
  - Python (`tree-sitter-python`)
  - JavaScript (`tree-sitter-javascript`)
  - TypeScript & TSX (`tree-sitter-typescript`)
  - Go (`tree-sitter-go`)
  - C & C++ (`tree-sitter-c`, `tree-sitter-cpp`)
- [x] Structural symbol extraction (functions, classes, interfaces, methods, signatures).
- [x] Intelligent code chunking bounded by line counts and AST symbol boundaries.

### Storage & Vector Retrieval
- [x] PostgreSQL relational models with SQLAlchemy 2.0 Async ORM.
- [x] 11 versioned Alembic database migrations.
- [x] Dense vector similarity storage with PostgreSQL `pgvector` (`vector(384)`).
- [x] Dual embedding engine:
  - Local ONNX-runtime `fastembed` (`BAAI/bge-small-en-v1.5`)
  - Cloud hosted Google Gemini Embedding API (`gemini-embedding-001`)
- [x] Hybrid search combining cosine vector similarity and SQL `ILIKE` lexical matching.

### Dependency Graph Intelligence
- [x] Static import resolution across multi-file codebases.
- [x] Dependency vertices (`dependency_nodes`) and directional edges (`dependency_edges`).
- [x] Blast radius calculation evaluating transitive change impacts.
- [x] Shortest dependency path discovery between arbitrary modules.
- [x] Interactive dependency graph visualization using `@xyflow/react` (React Flow 12).

### AI Engineering & Multi-Step Agent
- [x] Groq Cloud LPU integration with low-latency LLMs (`groq/compound-mini`, `groq/compound`, `meta-llama/llama-4-scout-17b-16e-instruct`).
- [x] Autonomous ReAct loop with multi-step tool execution (`search_code`, `read_file`, `get_dependencies`, `list_files`).
- [x] Automated Pull Request git diff analysis and risk assessment.
- [x] Automated documentation synthesis (`architecture`, `overview`, `api`, `onboarding`).

### UI & Developer Experience
- [x] Next.js 16 (App Router), React 19, and Tailwind CSS v4 web platform.
- [x] Secure HttpOnly cookie-based JWT authentication.
- [x] Unified RepoWeave rebranding across documentation and web interface.

---

## 2. In Progress (v1.3.0)

- [ ] **Streaming Agent Reasoning:** Real-time token and tool invocation streaming in the chat UI via Server-Sent Events (SSE).
- [ ] **Extended Language Coverage:** Tree-sitter parsers for Rust (`tree-sitter-rust`), Java (`tree-sitter-java`), and PHP (`tree-sitter-php`).
- [ ] **Private Repository Support:** Authenticated cloning via GitHub OAuth App integration and user Personal Access Tokens (PAT).
- [ ] **Multi-Branch Context:** Switching between branches without full repository re-ingestion.

---

## 3. Planned Initiatives (v2.0.0+)

### Automated GitHub Bot & CI/CD Integration
- [ ] **GitHub App Webhook Ingestion:** Automatically run architectural blast radius analysis and post AI review comments directly onto Pull Requests.
- [ ] **Breaking Change Alerts:** Automated alerts when PR diffs alter public function signatures with downstream dependents.

### Scalability & Infrastructure
- [ ] **Distributed Ingestion Workers:** Offload heavy AST parsing and batch embeddings to distributed Celery / Redis worker queues or Temporal workflows.
- [ ] **Cross-Repository Workspace Graphs:** Link multiple repositories into an interconnected microservices architecture graph.
- [ ] **Custom Enterprise LLM Endpoints:** Allow self-hosted vLLM or Ollama endpoints alongside cloud Groq LPUs.

### Enterprise Governance
- [ ] **Multi-Tenant Workspaces & RBAC:** Team-level permissions, workspace audits, and centralized billing.
- [ ] **SSO / SAML Authentication:** Enterprise identity integration with Okta, Azure AD, and Google Workspace.

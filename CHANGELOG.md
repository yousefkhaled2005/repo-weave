# Changelog

All notable changes to the RepoWeave platform will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.2.0] - 2026-11-03

### Changed
- **Platform Rebranding:** Transitioned project identity from GitSight to **RepoWeave** (*Understand. Connect. Engineer.*).
- **Documentation Overhaul:** Authored exhaustive documentation suite:
  - Technical architecture specification in `ARCHITECTURE.md` and `docs/architecture.md`.
  - Step-by-step setup and deployment guide in `docs/installation.md`.
  - Comprehensive REST API reference in `docs/api.md`.
  - Strategic milestone roadmap in `docs/roadmap.md`.
  - Project security and vulnerability reporting policy in `SECURITY.md`.
  - Development standards and contribution guide in `CONTRIBUTING.md`.
- **UI Identity:** Updated frontend metadata, navigation branding, login/register descriptions, and chat export defaults.
- **Developer Tooling:** Standardized dependency commands around `uv` across all backend documentation.

---

## [1.1.0] - 2026-03-15

### Added
- **Autonomous ReAct Agent:** Added multi-step reasoning agent using Groq LPUs with fallback support (`groq/compound-mini`, `groq/compound`, `meta-llama/llama-4-scout-17b-16e-instruct`).
- **Interactive Tools for Agent:** Enabled `search_code`, `read_file`, `get_dependencies`, and `list_files` agent tools.
- **Blast Radius Analysis:** Added graph calculation to evaluate downstream file and symbol impact of changes.
- **Pull Request Review Engine:** Automated git diff parsing and architectural risk assessment.
- **Documentation Synthesis:** AI-powered generation for architecture overviews, onboarding guides, and API specifications.
- **Dual Embedding Support:** Added hosted Google Gemini embedding API (`gemini-embedding-001`) alongside local ONNX `fastembed`.

---

## [1.0.0] - 2026-02-01

### Added
- **Multi-Language Tree-sitter Parsing:** AST parsing for Python, JavaScript, TypeScript, Go, C, and C++.
- **Code Chunking Engine:** Boundary-aware chunking preserving AST symbols and parent signatures.
- **Hybrid Search:** Fusion of cosine vector distance via `pgvector` (384-dimension) and SQL `ILIKE` lexical matching.
- **Dependency Graph:** Vertex and edge extraction for internal imports and external libraries.
- **Next.js 16 Web Interface:** React 19 App Router with Tailwind CSS v4 and `@xyflow/react` interactive graph visualization.
- **Database Architecture:** PostgreSQL models and 11 versioned Alembic migrations.
- **Authentication:** JWT sessions stored in HttpOnly cookies with role-based access control.

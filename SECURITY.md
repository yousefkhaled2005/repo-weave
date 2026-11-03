# Security Policy

RepoWeave takes the security of our platform and user codebases seriously. This document outlines our vulnerability disclosure process and the security controls implemented across the system.

---

## 1. Supported Versions

Security updates and patches are actively provided for the following versions:

| Version | Supported          |
| ------- | ------------------ |
| 1.2.x   | :white_check_mark: |
| 1.1.x   | :white_check_mark: |
| < 1.1.0 | :x:                |

---

## 2. Reporting a Vulnerability

If you discover a potential security vulnerability within RepoWeave, please do **not** disclose it publicly via GitHub issues or discussions.

Instead, please report the vulnerability responsibly:

1. **Email:** Send details to the maintainers at `yousefkhaledgouda2005@gmail.com` (or submit a Private Vulnerability Advisory on GitHub).
2. **Details to Include:**
   - A clear description of the vulnerability.
   - Exact steps or proof-of-concept (PoC) code to reproduce the issue.
   - Affected components (Frontend, Backend, Ingestion pipeline, Tree-sitter parsers, Database).
   - Any potential mitigations you have identified.
3. **Response Timeline:**
   - **Acknowledgment:** Within 48 hours of initial report.
   - **Triage & Assessment:** Within 5 business days.
   - **Remediation & Release:** Security patches will be prioritized and published promptly.

---

## 3. Platform Security Architecture

RepoWeave is engineered with several defense-in-depth security principles:

### 3.1 Authentication & Session Management
- **HttpOnly Cookies:** Authentication tokens are stored in `HttpOnly`, `SameSite=Lax` cookies, preventing client-side JavaScript access and neutralizing XSS token theft.
- **Password Security:** User passwords are hashed using industry-standard `bcrypt` with unique cryptographic salts.
- **Role-Based Access Control (RBAC):** Administrative endpoints require explicit `admin` role claims verified on every request.

### 3.2 Ephemeral Workspace Isolation
- Cloned repositories are isolated in ephemeral directories under the operating system temporary directory.
- Cloned repositories are accessed strictly within bounded execution paths; directory traversal attacks (`../`) are systematically blocked during AST parsing and file reads.
- Ephemeral workspaces are purged automatically after ingestion completes.

### 3.3 Database & Query Security
- All database interactions use SQLAlchemy 2.0 Async parameterized queries, completely mitigating SQL injection vulnerabilities.
- Vector embeddings and vector distance calculations are executed within parameterized functions provided by `pgvector`.

### 3.4 API Secret & Key Handling
- Service credentials (`GROQ_API_KEY`, `GOOGLE_API_KEY`, `GITHUB_TOKEN`, `SECRET_KEY`) are managed strictly through environment variables.
- Production environment checks prevent default or empty `SECRET_KEY` values from running in production (`ENV=production`).

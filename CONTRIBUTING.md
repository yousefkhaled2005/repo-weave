# Contributing to RepoWeave

Thank you for your interest in contributing to RepoWeave! This document explains how to set up your local development environment, follow project conventions, and submit high-quality contributions.

---

## 1. Getting Started

### 1.1 Fork and Clone

1. Fork the repository on GitHub.
2. Clone your fork locally:

```bash
git clone https://github.com/YOUR_USERNAME/repo-weave.git
cd repo-weave
```

3. Add the upstream remote:

```bash
git remote add upstream https://github.com/yousefkhaled2005/repo-weave.git
```

4. Configure environment files:

```bash
# Backend configuration
cp backend/.env.example backend/.env

# Frontend configuration
cp frontend/.env.example frontend/.env.local
```

5. Follow the setup instructions in [docs/installation.md](docs/installation.md) to install dependencies, run migrations, and launch the development servers.

---

## 2. Branch Naming Conventions

Create a dedicated branch from the latest `main`:

| Prefix | Use Case |
|--------|----------|
| `feat/` | New features or engine capabilities |
| `fix/` | Bug fixes and stability improvements |
| `docs/` | Documentation additions or corrections |
| `test/` | Test suite additions or coverage improvements |
| `refactor/` | Code refactoring without behavior change |
| `chore/` | Tooling, dependencies, or maintenance |

*Examples:* `feat/hybrid-search-rrf`, `fix/embedding-dimension-normalization`, `docs/api-guide`

---

## 3. Development Workflow

1. Sync your local `main` with upstream:

```bash
git fetch upstream
git checkout main
git merge upstream/main
```

2. Create your feature branch:

```bash
git checkout -b feat/your-feature-name
```

3. Make focused, well-documented changes. Keep pull requests scoped to a single concern.
4. Run tests and linters locally before submitting.
5. Push to your fork and open a pull request against `main`.

---

## 4. Coding Standards

### 4.1 Backend (Python 3.11)

- Managed with **uv**.
- Format with **Black** (line length 100).
- Sort imports with **isort**.
- Lint with **flake8**.
- Type hints on all function signatures.
- Adhere to the layered architecture: `endpoints -> services -> repositories -> models`.

```bash
cd backend
uv run black app tests
uv run isort app tests
uv run flake8 app tests
uv run pytest
```

### 4.2 Frontend (Next.js 16 / React 19 / TypeScript)

- Strict TypeScript mode.
- ESLint and Prettier for code quality and formatting.
- Follow existing component and hook patterns in `frontend/src`.

```bash
cd frontend
npm run lint
npm test
npm run build
```

---

## 5. Commit Message Guidelines

Use clear, imperative commit messages conforming to the Conventional Commits specification:

```
<type>: <short imperative summary>

[optional body explaining motivation and architectural context]
```

**Types:** `feat`, `fix`, `docs`, `test`, `refactor`, `chore`, `ci`

*Examples:*
- `feat: add Tree-sitter parser for Go language support`
- `fix: normalize Google Gemini embedding vector dimension to 384`
- `docs: update REST API specification with blast radius endpoints`

---

## 6. Submitting Pull Requests

1. Verify that all tests and linters pass locally.
2. Ensure no secrets, API keys, or `.env` files are included in the diff.
3. Include updated documentation or tests if behavior is modified.
4. Open a pull request against the `main` branch with a comprehensive summary of changes and testing steps.

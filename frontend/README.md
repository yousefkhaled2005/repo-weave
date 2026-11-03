# RepoWeave Frontend

**Product:** RepoWeave  
**Component:** Web Interface & Visualization Platform  
**Runtime:** Next.js 16 (App Router) / React 19 / TypeScript / Tailwind CSS v4  
**Graph Visualization:** `@xyflow/react` (React Flow 12)  
**State & Data Fetching:** TanStack Query v5  

---

## 1. Overview

The RepoWeave frontend provides a responsive, accessible interface for:
- Exploring ingested repository structure, files, and AST-parsed code chunks.
- Interactive dependency graph exploration with live node selection and path tracing.
- Context-aware AI chat with multi-step tool call inspection and reasoning history.
- Real-time hybrid code search across lexical and semantic indices.
- Pull request diff inspection and AI architectural reviews.
- Multi-document technical knowledge viewer (Architecture, API, Onboarding).

---

## 2. Local Development

### 2.1 Prerequisites
- Node.js 20+ (LTS) or Node.js 22+
- npm v10+

### 2.2 Setup Commands

```bash
# Copy environment configuration
cp .env.example .env.local

# Install dependencies
npm install

# Start development server
npm run dev
```

The application will be accessible at `http://localhost:3000`.

---

## 3. Environment Configuration

| Variable | Description | Default / Example |
|---|---|---|
| `NEXT_PUBLIC_API_URL` | Backend API URL. Set to backend service URL in production. | `http://localhost:8000` |
| `NEXT_PUBLIC_API_PROXY` | Set to `true` when hosted on Vercel to route API requests via Next.js `/api/*` rewrites. | `false` (local) / `true` (Vercel) |

---

## 4. Scripts

```bash
npm run dev      # Start development server
npm run lint     # Check ESLint rules
npm run test     # Run Vitest test suite
npm run build    # Build optimized production bundle
```

---

## 5. Deployment (Vercel)

1. Connect the GitHub repository to Vercel.
2. Set **Root Directory** to `frontend`.
3. Set Environment Variables:
   - `NEXT_PUBLIC_API_URL`: Your backend API URL (e.g., `https://repoweave-api.onrender.com`)
   - `NEXT_PUBLIC_API_PROXY`: `true`
4. Deploy. In-browser requests will route via same-origin Next.js rewrites to the backend API, preserving HttpOnly session cookies.

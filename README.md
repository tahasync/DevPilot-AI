# DevPilot AI

A Docker-based FastAPI + React/TypeScript local-first AI platform that authenticates via GitHub PAT/OAuth, syncs repos, generates READMEs/docs using pluggable LLM providers (Groq/OpenAI/Anthropic/OpenRouter), manages portfolios/resumes, generates social posts, analyzes skill gaps, creates learning plans, generates interview questions, finds open-source issues, and tracks analytics — all orchestrated via Docker Compose with 8 containers.

## What it does

DevPilot provides a dashboard where developers connect their GitHub account, then interact with AI modules for career management: generate READMEs for their repos, optimize their resume against job descriptions, draft LinkedIn/Twitter posts, analyze skill gaps, get learning plans, practice interviews, find good-first-issues, and view career analytics. Everything runs locally through Docker Compose — your data stays in your PostgreSQL/Redis/Qdrant stack.

**Note:** Some service modules are implemented as routers with thin business logic (resume optimizer, portfolio export, analytics). The Qdrant vector database container starts but nothing in the codebase references it yet. n8n workflow JSONs are basic.

## Tech stack

- **Backend:** Python 3.12, FastAPI, SQLAlchemy 2.0 async + asyncpg, Redis (hiredis), httpx, python-jose (JWT), passlib (bcrypt), structlog, Alembic
- **Frontend:** React 18, TypeScript 5, Vite 5, Tailwind CSS 3, React Router DOM 6
- **LLM providers:** Groq (default), OpenAI, Anthropic, OpenRouter — swappable via `.env`
- **Infrastructure:** PostgreSQL 16, Redis 7, Qdrant 1.12 (configured but unused in code), Docker Compose (8 containers + Nginx + pgAdmin), n8n

## Features

- **GitHub sync** — OAuth/PAT login, repo listing, stale detection, webhook support
- **README/document generation** — AI-generated README.md, CONTRIBUTING.md, ARCHITECTURE.md with review/approve/commit workflow
- **Portfolio export** — JSON/Markdown/HTML export of GitHub profile
- **Resume optimizer** — score and optimize resumes against job descriptions
- **Social post generator** — draft LinkedIn/Twitter posts
- **Skill gap analysis** — infer skills from repos, analyze gaps against target roles
- **Learning planner** — generate structured 4-week learning plans
- **Interview question generator** — role-specific questions with model answers
- **Open source issue finder** — search and bookmark good-first-issues matching your stack
- **Analytics dashboard** — career stats, language distribution, weekly reports
- **n8n automation** — scheduled health checks, stale detection (basic workflows)

## Setup

```bash
cp .env.example .env
# Edit .env — set LLM_PROVIDER and API key
docker compose up -d
# Open http://localhost:80
```

## Project structure

```
├── backend/app/
│   ├── application/services/   # 12 service modules (varying completeness)
│   ├── infrastructure/         # DB, GitHub client, LLM provider abstraction
│   ├── presentation/routers/   # REST endpoints
│   └── core/                   # Config, security, logging
├── frontend/src/
│   ├── pages/                  # 13 route pages
│   ├── components/             # Layout, ProtectedRoute
│   ├── api/                    # API client modules
│   └── context/                # Auth context
├── n8n/                        # 4 workflow JSONs (basic)
├── nginx/nginx.conf
├── docker-compose.yml          # 8 containers
└── .env.example
```

## Status

**Work in progress — mostly built.** The GitHub API client, LLM provider abstraction, auth system, and document generation are fully implemented. Several service modules have router scaffolding with thin business logic (skills, learning, interview, opensource, social, analytics). Qdrant is configured in Docker but unused in code. No tests. Not deployed — designed for local Docker use.
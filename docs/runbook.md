# Developer Runbook

This runbook explains how to stand up a local environment for the Meeting Minutes Management SaaS skeleton using the recommended stack (Next.js frontend and FastAPI backend). It emphasizes reproducibility via Docker Compose, environment configuration, and common operational tasks so contributors can "run it" quickly.

## 1) Prerequisites
- Docker and Docker Compose v2+
- Node.js 20+ and pnpm (if running the frontend outside containers)
- Python 3.11+ and uv or pip (if running the backend outside containers)
- An `.env` file populated from `.env.example` (see below)

## 2) Repository Layout (expected)
- `frontend/`: Next.js app with i18n (English/Arabic), TailwindCSS, and React Query.
- `backend/`: FastAPI service with Celery workers.
- `infra/`: Docker Compose and infrastructure configs.
- `docs/`: Planning documents (requirements, architecture, project plan, and this runbook).

> If these folders do not exist yet, scaffold them with the indicated tech choices before running the commands below.

## 3) Environment Variables
Create an `.env` at the repo root. At minimum, provide:

```
# Database
POSTGRES_USER=minutes
POSTGRES_PASSWORD=minutes
POSTGRES_DB=minutes

# S3-compatible storage (MinIO in local dev)
S3_ENDPOINT=http://minio:9000
S3_ACCESS_KEY=dev
S3_SECRET_KEY=devsecret
S3_BUCKET=minutes

# Auth & security
JWT_SECRET=change-me
OAUTH_GOOGLE_CLIENT_ID=your-google-client-id
OAUTH_GOOGLE_CLIENT_SECRET=your-google-client-secret

# AI providers
OPENAI_API_KEY=your-openai-key
WHISPER_API_KEY=your-whisper-key
```

## 4) Run with Docker Compose
1. Ensure the `infra/docker-compose.yml` file defines services for `frontend`, `backend`, `worker`, `db` (PostgreSQL), `redis`, and `minio`.
2. Build and start everything:

```
docker compose up --build
```

3. Access the apps:
   - Frontend: http://localhost:3000
   - Backend docs (OpenAPI): http://localhost:8000/docs
   - MinIO console: http://localhost:9001

4. Shut down and clean up:

```
docker compose down -v
```

## 5) Run Frontend Locally (optional)
```
cd frontend
pnpm install
pnpm dev
```

## 6) Run Backend Locally (optional)
```
cd backend
uv sync  # or: pip install -r requirements.txt
uvicorn app.main:app --reload
```

## 7) Common Operational Tasks
- **Apply database migrations**: `docker compose exec backend alembic upgrade head`
- **Run tests**: `docker compose exec backend pytest` and `docker compose exec frontend pnpm test`
- **Seed demo data**: `docker compose exec backend python scripts/seed.py`
- **Collect logs**: `docker compose logs -f backend worker frontend`

## 8) Troubleshooting
- Container fails to start: check `.env` values and rebuild (`docker compose build --no-cache`).
- Port conflicts: adjust published ports in `infra/docker-compose.yml`.
- AI calls failing: verify API keys and network egress; check Celery worker logs.
- PDF export rendering issues: ensure Chromium dependencies are present in the backend image for Puppeteer/wkhtmltopdf.

## 9) Production Notes (preview)
- Replace MinIO with AWS S3; provision RDS/PostgreSQL and Redis/ElastiCache.
- Use HTTPS with TLS termination (ALB/CloudFront); set secure cookies and CSP headers.
- Add observability sidecars/agents for OpenTelemetry traces and structured logging.
- Enable backups and retention for DB and storage; rotate secrets via AWS SSM.

By following this runbook, contributors can reliably start and observe the system in both containerized and local modes, satisfying the "run it" requirement from stakeholders.

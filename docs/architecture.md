# Architecture & System Design

This document outlines the proposed technical architecture, core services, data models, and delivery approach for the Meeting Minutes Management SaaS.

## 1. High-Level Architecture
- **Frontend**: Next.js + TypeScript + TailwindCSS; i18n (English/Arabic RTL); React Query for data; WebSockets for presence/editor collaboration; component library (Headless UI + Radix). PDF print view built with server-side HTML-to-PDF (Puppeteer/wkhtmltopdf).
- **Backend**: FastAPI (Python) for API and auth; Celery/Redis for async tasks (transcription, AI jobs, notifications); WebSocket gateway (uvicorn + Redis pub/sub) for real-time editor and notifications.
- **Database**: PostgreSQL with row-level multi-tenancy (tenant_id on all tables). Prisma or SQLAlchemy for ORM. Timescale/partitioning for event logs if needed.
- **Storage**: S3-compatible bucket for recordings, exports, and attachments; signed URLs for access.
- **AI Layer**: Whisper API for STT; OpenAI GPT for summarization/classification/assistance; LangChain for prompt orchestration; retries and fallbacks configured.
- **Integrations**: Calendar (Google/Outlook), Slack/Teams webhooks, Google Drive/OneDrive storage adapters, Zapier webhooks.
- **Deployment**: Docker images; CI/CD via GitHub Actions; environments for dev/stage/prod; infra on AWS (ECS/Fargate or EKS) with ALB; CloudFront for static assets; secrets via AWS SSM; monitoring with OpenTelemetry + Grafana + Loki.

## 2. Service Components
- **API Gateway**: Auth, RBAC enforcement, request validation, rate limiting.
- **Auth Service**: JWT issuance/refresh, OAuth flows, 2FA, password reset, organization/user provisioning.
- **Meetings Service**: Scheduling, attendee management, integration links, recording metadata, meeting documents, PDF generation endpoints.
- **Transcription Worker**: Handles uploads/live stream chunks, calls Whisper/Google STT, writes transcripts with diarization/timestamps.
- **Minutes/Editor Service**: Collaborative CRDT/OT backend for sections (Decisions/Notes/Action Items/Questions); version history and audit events.
- **Tasks Service**: CRUD with workflow states, custom fields, saved views, Kanban/list/calendar feeds, reminders, archival.
- **Dashboard Service**: Widget registry and layout storage; pulls aggregates from meetings/tasks AI summaries.
- **Notification Service**: In-app feed (WebSocket), email (SES), Slack/Teams webhooks, digest generation.
- **AI Service**: Abstraction around LLMs; prompt templates; caching; safety/PII guardrails; asynchronous job queue for long-running tasks.
- **Reporting Service**: Weekly/monthly report generation; exports (PDF/Word/Excel); templated branding.

## 3. Data Model (Key Tables)
- **Organization**(id, name, locale, branding, plan, settings)
- **User**(id, email, name, locale, timezone, org_id, role, avatar, preferences)
- **Team/Department**(id, org_id, name, description)
- **Membership**(user_id, team_id, role)
- **Meeting**(id, org_id, team_id, title, agenda, start_at, end_at, recurrence, location/link, status)
- **Attendee**(meeting_id, user_id/email, role, response)
- **Recording**(id, meeting_id, storage_url, duration, transcript_status)
- **Transcript Segment**(id, recording_id, speaker, start_ts, end_ts, text)
- **Minutes Document**(id, meeting_id, section, content, version, created_by)
- **Task**(id, org_id, meeting_id?, title, description, owner_id, due_date, priority, status, tags, archived, custom_field_values)
- **Custom Field**(id, org_id, name, type, options, order, visibility)
- **Task View**(id, owner_id/team_id, config JSON for columns/filters/sorts/layout)
- **Notification**(id, org_id, user_id, type, payload, channel, read_at)
- **Integration Account**(id, org_id, provider, tokens, scopes, metadata)
- **Audit Log**(id, org_id, actor_id, action, resource, timestamp, ip)

## 4. API Surface (sample)
- Auth: `/auth/register`, `/auth/login`, `/auth/oauth/{provider}/callback`, `/auth/refresh`, `/auth/2fa`.
- Users/Orgs: `/orgs`, `/teams`, `/users`, `/invites`.
- Meetings: `/meetings`, `/meetings/{id}`, `/meetings/{id}/recordings`, `/meetings/{id}/minutes`, `/meetings/{id}/pdf`.
- Tasks: `/tasks`, `/tasks/views`, `/tasks/{id}`, `/tasks/{id}/archive`, `/tasks/{id}/assignee`.
- Dashboards: `/dashboards`, `/dashboards/{id}/widgets`.
- Notifications: `/notifications`, `/subscriptions`.
- AI: `/ai/summarize`, `/ai/classify`, `/ai/suggest`, `/ai/analytics`.
- Integrations: `/integrations/{provider}/connect`, `/integrations/{provider}/webhook`.

## 5. AI Workflows
- **Transcription**: Upload triggers job -> STT provider -> store segments with timestamps -> emit events to editor.
- **Summarization**: On meeting end or on-demand; uses transcript/minutes to generate decisions, tasks, questions, key points.
- **Classification**: Real-time classification per note to auto-place content; users can override.
- **Task Insights**: Periodic job computes completion/overdue trends; feeds dashboards and digests.
- **Guardrails**: Tenant-specific context; no data mixing; PII redaction; monitoring of token usage and failures.

## 6. Security & Compliance
- JWT with refresh rotation; role/tenant checks on every request.
- Encrypted storage (S3 SSE); HTTPS everywhere; CSP/helmet headers.
- Audit logs for data exports, role changes, integration token access.
- DDoS/rate limiting at gateway; bot protection for auth flows.
- Backups, retention controls, right-to-be-forgotten workflows.

## 7. Deployment & DevOps
- **CI/CD**: Lint, tests, type checks, container builds, IaC validation; trunk-based branching.
- **IaC**: Terraform for AWS (VPC, RDS, S3, ECS/EKS, CloudFront, ALB, SSM, SES, ElastiCache). Secrets via SSM Parameter Store.
- **Observability**: OpenTelemetry tracing; structured logs; metrics dashboards; synthetic checks for ingest and PDF export.
- **Environments**: Dev/stage/prod with seeded demo org for QA.

## 8. Delivery Plan (6–8 Week MVP)
- **Week 1**: Setup repo, CI/CD, auth skeleton, tenant/org models, basic UI shell with i18n.
- **Week 2**: Meetings CRUD, calendar integration stubs, file upload to S3, transcription worker plumbing.
- **Week 3**: Collaborative editor MVP with sections and timestamps; task CRUD + Kanban/list; reminders email only.
- **Week 4**: AI summary/classification flows; dashboard widgets for meetings/tasks/summary.
- **Week 5**: PDF export template with branding and RTL; Slack notifications; calendar two-way sync beta.
- **Week 6**: Reports (weekly digest), custom task fields/views, performance hardening, QA, accessibility pass.
- **Week 7–8 (buffer)**: Bug fixes, polish, staging perf tests, production checklist.

## 9. Future Enhancements
- Subscription billing (Stripe), usage metering, seat management.
- Live audio streaming to STT; speaker diarization improvements.
- AI coaching for meeting quality; topic modeling; sentiment.
- Advanced permissions (field-level), data residency options.
- Offline-capable editor; mobile apps.


# Project Plan & Milestones

This plan translates the scope into a deliverable roadmap with milestones, responsibilities, and quality checks.

## Milestones
1. **Foundation (Week 1)**
   - Repo, CI/CD, environment configs, base Next.js + FastAPI skeleton.
   - Auth flows (email/password, OAuth skeleton), org/team models.
   - Definition of Done (DoD) and coding standards, monitoring setup.

2. **Meetings & Transcription (Weeks 2–3)**
   - Meeting scheduling CRUD with calendar integration stubs.
   - File upload to S3; transcription worker to Whisper/Google STT.
   - Real-time progress updates; transcript storage with timestamps.

3. **Minutes Editor & Tasks (Weeks 3–4)**
   - Collaborative editor (CRDT/OT) with Decisions/Notes/Action Items/Questions.
   - Task CRUD with Kanban/list/calendar; reminders via email.
   - Linking tasks to meetings and attachments; audit trail for edits.

4. **AI Layer & Dashboards (Weeks 4–5)**
   - AI summary/classification endpoints; weekly smart summary job.
   - Dashboard widgets for meetings, tasks, completion stats; drag-and-drop layout persistence.

5. **Exports & Notifications (Week 5)**
   - PDF export with branding + RTL/LTR support.
   - Slack/Teams notifications, digests, and escalation rules.

6. **Reports, Custom Fields, Hardening (Week 6)**
   - Weekly/monthly reports; custom task fields and saved views.
   - Performance pass (p95 < 3s key pages); accessibility sweep; security review.

7. **Stabilization & Launch (Weeks 7–8 buffer)**
   - Bug fixes, staging load test, production checklist, playbooks.

## Risks & Mitigations
- **AI dependency latency/cost**: Cache summaries; batch jobs; fallback providers.
- **Collaboration complexity**: Use proven CRDT/OT library; aggressive integration tests; feature flags.
- **Multi-tenancy security**: Row-level filters; automated authorization tests; audit logging; static analysis.
- **Integration fragility**: Use provider webhooks with retries; observability dashboards; sandbox accounts.
- **PDF/RTL rendering**: Snapshot tests, visual QA; choose renderer with RTL support (Puppeteer + CSS logical props).

## Quality & Delivery Controls
- DoD per story: unit/integration tests, lint/type checks, accessibility checks for UI, security review for auth flows.
- CI gates: PR lint/tests, container scan, IaC validation; required approvals.
- Environments: Dev/stage with seeded demo data; feature flags for risky changes.
- Documentation: API reference (OpenAPI), runbooks for operations, playbooks for incidents.


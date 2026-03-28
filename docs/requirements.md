# Meeting Minutes Management SaaS – Requirements

This document captures the full scope and functional breakdown for the Smart SaaS platform requested. It refines the user story into implementable requirements and acceptance criteria that will guide design, build, and QA.

## 1. Users and Roles
- **Authentication**: Email/password with JWT; OAuth (Google/Microsoft). Optional 2FA.
- **Roles**: Admin, Organizer, Member, Viewer.
- **Organizations**: Org account with departments/teams; per-tenant data isolation.
- **Invitations**: Email-based invites with role assignment; pending/accepted tracking.
- **RBAC**: Permissions for meeting creation, recording upload, editing minutes, task edits, dashboard configuration, export, integrations.
- **Profile and preferences**: Locale (Arabic/English), timezone, notification channels (email, Slack, Teams), working hours.

## 2. Meetings and Minutes
- **Scheduling**: Create meetings with title, agenda, date/time, timezone, recurrence, associated team/project, attendees (internal/external), location/link (Teams/Meet/Zoom/manual).
- **Recording**: Live audio capture or file upload (mp3/m4a/wav). Track duration, owner, and storage location.
- **Transcription**: Whisper/Google STT; diarization with speaker attribution; timestamps per segment.
- **Editor**: Collaborative rich-text editor with real-time presence. Sections: Decisions, Notes, Action Items, Questions. Per-item timestamps and speaker tags. Version history and change tracking.
- **Linkage**: Meeting to tasks, departments, projects, dashboards. Attachments (docs, images).
- **PDF export**: Formal template with logo, date, attendees, content, task list; LTR/RTL support; configurable branding; one-click export.

## 3. Tasks / Action Items
- **Creation**: From minutes or standalone. Fields: title, description, owner, due date, priority, status (New, In Progress, Completed, Deferred), tags, attachments, linked meeting.
- **User controls**: View by list/Kanban/calendar; edit fields; reassign within team; close with completion notes; archive.
- **Flexible table**: Add/delete/reorder columns; column types (text/date/dropdown/number/status); personal and team views; saved filters/sorts; drag-and-drop column ordering.
- **Reminders**: Configurable auto-reminders via email/Slack/Teams. Overdue escalation.

## 4. AI Features
- **Summaries**: Generate concise meeting summaries and weekly smart summary per team.
- **Classification**: Categorize content as Decision/Task/Question/Info; suggest section placement.
- **Writing assistance**: Suggestions, grammar/style improvements, clarity prompts inside editor.
- **Analytics**: Completion trends, overdue/at-risk detection, meeting quality insights.
- **Data privacy**: Per-tenant prompts; no cross-tenant training; configurable retention.

## 5. Dashboards
- **Personal**: Upcoming meetings, tasks by status, completion rate, reminders, AI weekly summary.
- **Team/Project**: Modular widgets (meetings, workload, burndown, overdue items, summary). Drag-and-drop layout; saved layouts per team.
- **Permissions**: Widget-level visibility based on role and membership.

## 6. Notifications and Reports
- **Notifications**: In-app, email, Slack/Teams webhooks; real-time for task assignment, due reminders, mentions, meeting edits.
- **Reports**: Weekly/monthly task progress, meeting activity, exportable to PDF/Word/Excel.
- **Digest**: AI-generated digest with highlights and risks.

## 7. Integrations
- **Calendars**: Outlook/Google Calendar sync (two-way for events/attendees/links).
- **Meetings**: Microsoft Teams/Google Meet linking for recordings and presence.
- **Storage**: Google Drive/OneDrive/S3 for recordings and exports.
- **Automation**: Slack/Zapier actions (create task, post summary, reminders).

## 8. Localization & Accessibility
- **Languages**: English and Arabic (RTL/LTR), UI toggle, locale-based formatting.
- **Accessibility**: WCAG 2.1 AA for UI, keyboard navigation, captions for audio.

## 9. Non-Functional Requirements
- **Performance**: Sub-3s p95 for dashboard/task list; streaming transcription; scalable ingestion for concurrent meetings.
- **Security**: GDPR-ready, encryption in transit/at rest, audit logs for sensitive actions, role-based data filters, rate limiting.
- **Reliability**: 99.9% availability target; backups for DB/files; health checks; circuit breakers around AI providers.
- **Scalability**: Multi-tenant design; horizontal scaling for API, worker, and websocket nodes.

## 10. Success Criteria for MVP (6–8 Weeks)
- Role-based login and invitations.
- Meeting creation with upload or link; transcription available.
- Collaborative minutes editor with Decisions/Notes/Action Items sections and timestamps.
- Task creation from minutes; Kanban/list view; reminders via email.
- AI summary of minutes and weekly summary; classification of content items.
- Dashboard with configurable widgets for meetings/tasks/summary.
- PDF export of minutes with branding and RTL/LTR.
- Calendar and Slack integration for links/notifications.


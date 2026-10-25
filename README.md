# SmartFlood V4 — Cloud Run Backend and Application Migration

SmartFlood is an AI-assisted flood monitoring and disaster-relief coordination platform.

This repository contains the SmartFlood V4 application source and documentation associated with backend deployment and frontend presentation migration. The application includes a Next.js frontend, a Next.js API service, and a Python FastAPI AI service.

The V4 work aims to preserve existing API contracts, authentication and session behavior, role-based access control (RBAC), database integrations, and AI-assisted relief workflows while improving the frontend presentation and deployment architecture.

## Architecture

```text
Browser
   |
   v
SmartFlood Frontend
Next.js · React · TypeScript
   |
   | /api/* requests
   v
Backend API
Next.js Route Handlers
   |
   +----> Supabase / PostgreSQL
   |
   +----> MongoDB Atlas
   |
   +----> AI Service
          Python · FastAPI
          Relief recommendations
```

The frontend provides the application interface. The backend API handles application endpoints, server-side authentication and authorization, data access, relief workflows, and communication with the AI service.

The AI service provides recommendation functionality and related relief-allocation logic.

## Core Application Areas

- Flood monitoring, dashboards, sensors, and flood history.
- Resident and household management.
- AI-assisted relief recommendations and allocation.
- Relief inventory, campaign management, and distribution workflows.
- Notifications, verification, audit-related views, and reporting.
- Role-aware navigation and administrative functions.

## Technology Stack

| Component | Technologies |
|---|---|
| Frontend | Next.js, React, TypeScript |
| Backend API | Next.js Route Handlers, TypeScript |
| AI service | Python, FastAPI |
| Databases | Supabase / PostgreSQL and MongoDB |
| Deployment target | Google Cloud Run |
| Container and build tooling | Must be verified against the selected deployment branch |

## Repository Structure

```text
.
├── Frontend/           # Application interface
├── Backend/
│   ├── api/            # Backend API and server-side workflows
│   └── ai/             # AI service
├── supabase/           # Migration references
├── docs/               # Architecture and API documentation
├── README.md
└── Frontend/
    ├── MIGRATION_REPORT.md
    └── UI_PARITY_AUDIT.md
```

Use the checked-out branch as the source of truth for the exact files available.

## Local Development

Run each service in its own terminal. Confirm its package scripts and lockfile before installing dependencies.

### Backend API

```bash
cd Backend/api
npm install
npm run dev
```

The current API development script uses port `5000`.

### AI service

```bash
cd Backend/ai
python3 -m venv .venv
.venv/bin/python -m pip install -r requirements.txt
.venv/bin/python -m uvicorn app.main:app --reload --port 8000
```

Run the frontend separately from `Frontend/` and configure its API target to reach the backend.

## Google Cloud Run Deployment

The V4 deployment work targeted Google Cloud Run in the `asia-southeast1` region. The previously investigated API service was named `smartflood-api`.

The API's current package configuration uses `next start --port 5000`. Cloud Run's configured container port must match the port on which the application actually listens.

A previous deployment troubleshooting attempt encountered a container-port mismatch. Before attempting another deployment, verify the start command, container port, runtime `PORT` configuration, and latest service revision. If the application is changed to use the runtime-provided `PORT`, ensure the start command and deployment configuration are updated consistently.

### Deployment verification checklist

1. Confirm the latest Cloud Run revision starts successfully and receives traffic.
2. Verify the API liveness endpoint, `/api/health`, if present in the deployed revision.
3. Confirm required environment variables and secrets are configured.
4. Test database connectivity and API-to-AI communication.
5. Verify authentication, role permissions, and critical relief workflows.
6. Review application logs for startup failures and runtime errors.

A successful liveness response does not prove database readiness, authenticated workflow correctness, or full production readiness. Record the revision and actual test results before describing a deployment as operational.

## Environment and Secrets

Use the tracked `.env.example` files, where present, to identify required settings for each service.

Depending on the service, configuration may include database URLs and credentials, Supabase server-side credentials, a session-signing secret, the AI service URL, CORS allowlist values, and a runtime port.

Keep private values in server-side configuration or an approved secret manager. Never commit real `.env` files or expose privileged credentials through `NEXT_PUBLIC_*` variables.

## Frontend Migration and Validation

The repository documents the migration of the frontend presentation while preserving existing backend contracts and application workflows.

The migration report records source-level and automated validation as completed at the time of reporting. However, it also states that rendered visual acceptance and authenticated end-to-end verification remained pending.

See:

- [Frontend Migration Report](Frontend/MIGRATION_REPORT.md)
- [Frontend UI/API Parity Audit](Frontend/UI_PARITY_AUDIT.md)
- [Architecture Documentation](docs/ARCHITECTURE.md)

These reports describe the scope and limitations of the migration. Source-level checks should not be interpreted as proof of pixel-perfect visual parity or successful live transactions.

## Security Considerations

- Enforce RBAC and data-access scope on the server.
- Do not trust authorization decisions supplied solely by the browser.
- Validate requests and protect session credentials.
- Restrict CORS to the intended frontend origins.
- Use least-privilege service identities and secret-management practices.
- Review audit logging for sensitive administrative and relief-distribution operations.

## Project Status

SmartFlood V4.1 is the application baseline for continued deployment and migration work. Confirm current Cloud Run health and application-level testing independently; historical deployment attempts and migration reports must not be presented as evidence of current production readiness.

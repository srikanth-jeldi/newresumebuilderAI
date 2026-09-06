# Backend/Web Engineer — First Assigned Task

Task ID: `DEV1-001`

Project: **Resumaire — An EpitomeHub® Product**

Status: `DEV_READY`

## Objective
Create the initial backend/web foundation that proves the approved architecture without implementing the full product at once.

## Scope

### Backend foundation
Create the Maven/service skeletons for:
- `api-gateway`
- `identity-service`
- `resume-service`
- `ai-career-service`
- `career-tracking-service`

Use:
- Java 21
- Spring Boot 4.1.1
- Spring Cloud 2025.1.2 where applicable
- Maven
- Actuator
- validation
- PostgreSQL/Flyway setup per service

### Database foundation
Prepare one PostgreSQL database with service-owned schemas:
- `identity`
- `resume`
- `ai_career`
- `career_tracking`

Each service must have isolated DB credentials/configuration and its own Flyway migration location/history.

### Local infrastructure
Add Docker Compose for:
- PostgreSQL
- MinIO
- backend services as they become buildable
- document conversion worker placeholder/container contract

Do not add Kafka, Redis or Kubernetes.

### Identity slice
Implement the first production-style vertical slice:
- user registration
- login
- JWT access token
- rotating refresh token
- logout/revocation
- `GET /api/v1/me`

Include ownership/security foundations for downstream services.

### Gateway slice
Configure:
- routing to Identity Service
- protected/public route policy
- correlation/request ID
- CORS configuration driven by environment
- JWT validation pattern for protected routes

### Web foundation
Create the Next.js + React + TypeScript + Tailwind app with:
- Resumaire branding
- `An EpitomeHub® Product`
- landing-page shell
- register page
- login page
- authenticated dashboard shell
- protected-route/session handling foundation

Do not implement every resume-builder screen in this task.

### CI foundation
Add initial GitHub Actions PR checks for:
- Java/Maven build and tests
- web lint/typecheck/build
- Docker build validation where practical

## Acceptance Criteria
1. Repository contains buildable backend service skeletons.
2. Identity Service registration/login/refresh/logout/me works through API Gateway.
3. Passwords are securely hashed and never returned/logged.
4. Access tokens expire according to configuration; refresh tokens rotate and are stored hashed.
5. PostgreSQL schemas are created/migrated by Flyway.
6. No service accesses another service's tables.
7. Next.js app runs and exposes Resumaire landing/login/register/dashboard shell.
8. Protected dashboard redirects/rejects unauthenticated access appropriately.
9. Docker Compose starts the required local infrastructure.
10. Maven and web build checks pass.
11. Automated tests cover authentication success/failure and refresh/logout behavior.
12. No secrets are committed.

## Out of Scope
- full Resume CRUD
- resume upload/parsing
- DOCX editing
- ATS generation
- AI integration
- billing checkout
- Flutter app
- full template catalog

These will be subsequent tasks.

## Required Completion Report
Use the format in `agents/backend-web/SYSTEM.md`.

## Handoff
When acceptance criteria are met, mark:

`DEV_COMPLETE`

and prepare the feature for QA. Do not self-approve the release.

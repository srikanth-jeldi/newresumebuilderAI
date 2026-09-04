# New Resume Builder AI — Architecture Baseline

Status: DRAFT — to be refined by ARCH-001.

## Architecture Direction
Start with a modular monolith backend rather than microservices. The project is new and there is no demonstrated scale or team boundary that justifies distributed-service operational complexity.

```text
Web Client -----------\
                      \
Flutter Android/iOS ---> HTTPS API
                         |
                         v
               Modular Backend Application
               | Identity
               | Resume
               | Template
               | Export
               | AI Assistance
               | ATS Analysis
               | Usage/Subscription (later/MVP if required)
                         |
             +-----------+-----------+
             |                       |
          Relational DB          Object Storage
                                     |
                               Generated/private files

Backend AI module ---> AI Provider Adapter ---> Model Provider
          |
          +--> Structured output validation / timeout / error policy
```

## Recommended Logical Modules
### Identity
Users, authentication, session/token lifecycle and access policy.

### Resume
Resume aggregate, section ordering, ownership and version/update semantics.

### Template
Supported template metadata and rendering configuration.

### Export
Generate PDF from validated persisted resume data. Long-running generation can become asynchronous only if measurements justify it.

### AI Assistance
Provider abstraction for rewriting/suggestions. AI responses are validated and never directly mutate user data without an explicit save action.

### ATS Analysis
Combines deterministic extraction/matching where practical with AI-generated qualitative suggestions. Persist analysis history only when product requirements justify it.

## Data Store
Use one relational database for transactional product data in MVP. PostgreSQL or MySQL are both valid; choose one in ADR-001 based on the team's implementation stack and hosting constraints.

## Object Storage
Use private object storage for generated PDFs or uploaded files that must persist. Access should use authenticated application delivery or short-lived signed URLs, not permanent public URLs for private resumes.

## API
Version API paths (for example `/api/v1`). Web and mobile share contract semantics.

## Authentication
Exact mechanism is an implementation decision to finalize in ARCH-001, but server-side ownership checks are mandatory on every private resume/resource operation.

## AI Reliability
- Provider timeout
- bounded retries only where safe
- structured/schema validation
- cost/token limits
- model/provider configuration outside application code
- graceful user-facing failure
- no secret or full sensitive content logging

## Observability Baseline
- structured application logs
- request/correlation ID
- health/readiness endpoint
- latency/error metrics
- AI provider latency/error/cost indicators

## Deployment Baseline
Initial deployment should prefer few independently operated components:
- web client hosting
- backend application
- relational database
- object storage
- external AI provider

Do not add Kubernetes for the MVP unless the hosting environment or operational requirements justify it.

## Open Decisions for ARCH-001
1. Backend language/framework and exact versions
2. Relational database selection
3. Authentication/token/session model
4. PDF rendering approach
5. Object-storage provider
6. AI provider abstraction and supported model policy
7. Hosting/deployment target
8. Subscription/usage metering MVP scope

# ADR-003 — Service Communication and Async Processing

Status: **ACCEPTED**

## Context
Resumaire has both low-latency request/response operations and potentially long-running work such as resume parsing, AI tailoring, ATS analysis, DOCX mutation, PDF rendering, and document conversion.

Putting all of this behind a single synchronous HTTP request would create poor timeout behavior, duplicate work on retries, unclear progress, and fragile UX. At the same time, introducing Kafka/RabbitMQ at MVP stage would add infrastructure and operational complexity before throughput justifies it.

## Decision
Use **synchronous REST** for normal service-to-service business operations and a **database-backed durable job model + worker pattern** for long-running document/AI workflows.

Do **not** introduce Kafka, RabbitMQ, or another broker for the MVP unless a later measured requirement justifies it.

## Synchronous REST
Use REST for operations expected to complete quickly and where the caller needs an immediate response.

Examples:
- authentication and token refresh
- user/profile reads
- resume metadata CRUD
- template catalog reads
- application tracker CRUD
- project-selection updates
- retrieving ATS/interview results already generated
- short AI operations that are explicitly proven to stay within an acceptable latency budget

Rules:
- define connect/read timeouts
- use correlation/request IDs
- propagate authenticated user context only when required
- use service credentials for machine-to-machine calls without an end-user principal
- downstream service validates caller + authorization
- avoid unbounded retry chains
- never retry non-idempotent writes blindly

## Async Job Processing
Use a durable job when work can be slow, CPU/memory intensive, dependent on AI/provider latency, or unsafe to hold open behind a browser/mobile request.

Initial job types:
- `RESUME_PARSE`
- `JD_PARSE`
- `ATS_ANALYSIS`
- `AI_TAILORING`
- `DOCX_APPLY_CHANGES`
- `DOCX_EXPORT`
- `PDF_EXPORT`
- `COVER_LETTER_GENERATION`
- `INTERVIEW_PREP_GENERATION`

A job may remain internal to the owning service. Do not create a central shared job database across services.

## Job State Model
Use an explicit state machine:

```text
QUEUED
  ↓
RUNNING
  ├──> SUCCEEDED
  ├──> FAILED
  └──> CANCELLED

Optional retry path:
FAILED_RETRYABLE → QUEUED
```

Recommended fields:
- `id`
- `owner_user_id`
- `job_type`
- `status`
- `progress_percent` or coarse progress stage
- `attempt_count`
- `max_attempts`
- `idempotency_key` where applicable
- `input_reference`
- `result_reference`
- `error_code`
- `error_summary` (non-sensitive)
- `created_at`
- `started_at`
- `completed_at`
- `next_attempt_at`
- `version` for optimistic locking

Do not store full private resume/JD payloads inside operational error text.

## Worker Claiming
Workers must claim jobs safely so multiple instances do not process the same job concurrently.

For PostgreSQL, use an atomic claim strategy such as row locking / `FOR UPDATE SKIP LOCKED`, or another proven equivalent.

Job handlers must be written to tolerate duplicate delivery/claim recovery where practical.

## Idempotency
Operations that create durable outputs must avoid duplicate artifacts when a client retries.

Examples:
- export creation
- AI tailoring request
- ATS run

Use an idempotency key or an equivalent request-deduplication key where the workflow can be retried from the client/gateway.

## Retry Policy
Retry only transient failures.

Retryable examples:
- temporary AI provider timeout
- temporary object-storage failure
- renderer/container temporary unavailable

Non-retryable examples:
- unsupported document format
- corrupt/password-protected file where unsupported
- failed ownership/authorization
- invalid user input

Use bounded attempts and exponential/backoff scheduling. No infinite retry loops.

## Client Progress Contract
Long-running operations return quickly with a job reference.

Example:

```http
POST /api/v1/resumes/{resumeId}/ats-analyses
202 Accepted
```

```json
{
  "jobId": "...",
  "status": "QUEUED",
  "statusUrl": "/api/v1/jobs/..."
}
```

Initial MVP client update mechanism:
- polling with sensible interval/backoff

Later, if UX requires it:
- Server-Sent Events or WebSocket notifications may be added for progress updates

Do not require WebSockets for the first implementation.

## Ownership and Security
Every job is user-owned or service-owned.

For user-facing jobs:
- only the owning user (or authorized admin role) may read status/results
- changing a job ID must never expose another user's job
- result artifacts remain private

Worker credentials must be separate from end-user credentials.

## Transaction Boundary
When a request creates a job, persist the job in the same transaction as the state that makes the job valid where possible.

Workers update job state and domain/output state transactionally where appropriate.

Avoid distributed transactions across microservices.

## Failure Recovery
A worker crash must not leave a job permanently stuck in `RUNNING`.

Implement a lease/heartbeat or stale-running recovery rule. A scheduled recovery task may re-queue jobs whose lease expired, subject to max-attempt and idempotency rules.

## Why Not Kafka/RabbitMQ Yet
A broker is not rejected permanently. It is deferred because MVP does not yet have measured throughput or integration requirements that justify another operational dependency.

Introduce a broker later when one or more are true:
- DB-backed queue becomes a measurable bottleneck
- cross-service event fan-out is required
- high-throughput background processing needs independent scaling
- durable event integration with external systems becomes important
- retry/dead-letter operations become materially easier with a broker

If introduced later, migrate behind the job abstraction rather than rewriting product workflows.

## Consequences
### Positive
- much simpler MVP operations
- durable long-running workflows
- clear progress and retry semantics
- fewer HTTP timeout failures
- supports multiple workers and horizontal scaling
- easy to observe in PostgreSQL

### Negative
- polling adds some client/server traffic
- DB-backed queue requires careful locking/indexing
- not ideal for very high message throughput
- job recovery/idempotency must be implemented correctly

## Required QA
QA must test:
- duplicate client submissions
- same job cannot be processed concurrently by two workers
- worker crash/recovery
- retryable vs non-retryable failures
- max retry limit
- user A cannot read user B's jobs/results
- client refresh/navigation does not lose job state
- job success does not create duplicate document artifacts
- stale `RUNNING` recovery

## Decision Summary
**REST for fast request/response operations. Database-backed durable jobs + workers for slow processing. No message broker in MVP.**

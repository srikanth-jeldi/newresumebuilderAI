# ADR-008 — PostgreSQL Schema Isolation Strategy

Status: ACCEPTED

## Context
Resumaire uses a small microservice architecture but must remain inexpensive and operationally simple for the startup/MVP stage. Running a separate managed PostgreSQL instance for every service would increase cost and operational overhead without providing enough early-stage value.

## Decision
Use **one PostgreSQL server/cluster with one database and a separate schema per service** for MVP and early production.

Initial schemas:
- `identity` — Identity Service
- `resume` — Resume Service
- `ai_career` — AI Career Service persisted metadata/jobs/usage where required
- `career_tracking` — Career Tracking Service

Each service:
- connects with its own database user/role where practical
- has privileges only on its own schema
- owns its Flyway migrations
- may not directly query, insert, update or delete another service's tables
- may not create ORM entity relationships across service boundaries

## Why This Is the Best Startup Choice
- One managed PostgreSQL instance keeps infrastructure cost low.
- Backups, monitoring and upgrades are simpler.
- Schema isolation preserves clear service ownership.
- Future migration to separate databases/instances remains possible because direct cross-schema coupling is prohibited.

## Access Model
Recommended roles:
- `resumaire_identity_app` -> USAGE + DML only on `identity`
- `resumaire_resume_app` -> USAGE + DML only on `resume`
- `resumaire_ai_career_app` -> USAGE + DML only on `ai_career`
- `resumaire_career_tracking_app` -> USAGE + DML only on `career_tracking`

Migration credentials may have DDL permissions for the service-owned schema, but application runtime credentials should use the minimum required privileges.

Do not use one superuser credential for every service in production.

## Cross-Service Data
If Resume Service needs user information from Identity Service, it uses the agreed service API/contract, not a SQL join against `identity` tables.

If Career Tracking needs a resume-version reference, it stores only the external identifier required by its domain and validates/reference-resolves through APIs when necessary.

No foreign keys across service schemas are required in the initial architecture because they would create database-level coupling between independently owned service domains.

## Transactions
Transactions are local to one service/schema boundary.

Do not attempt distributed database transactions across services.

For workflows spanning services:
- use synchronous REST for short request/response operations
- use the durable job/workflow approach already defined for long-running processing
- design operations to be idempotent where retries can occur

## Flyway
Each service owns an independent Flyway history table in its schema.

Examples:
- Identity Service -> `identity.flyway_schema_history`
- Resume Service -> `resume.flyway_schema_history`
- AI Career Service -> `ai_career.flyway_schema_history`
- Career Tracking Service -> `career_tracking.flyway_schema_history`

A service deployment may migrate only its own schema.

## Local Development
Docker Compose may start one PostgreSQL container/database and initialize all service schemas/users.

This mirrors the logical production ownership model while remaining easy to run locally.

## Backups
The PostgreSQL instance is backed up as one unit according to ADR-006.

Restore procedures must verify that all schemas and Flyway histories restore consistently.

## Scaling / Future Split
Move a service to its own database or PostgreSQL instance only when justified by measured needs such as:
- independent scaling or availability requirements
- heavy workload isolation
- regulatory/data-boundary requirements
- backup/restore independence
- noisy-neighbor problems
- material team/ownership separation

Because direct cross-schema SQL is forbidden, this split should be primarily a connection/configuration and migration exercise rather than a major application rewrite.

## Consequences
### Positive
- lowest practical database cost
- simple operations and backups
- clear service ownership
- fewer network/database endpoints
- easy local development
- preserves future separation path

### Negative
- one PostgreSQL cluster remains a shared infrastructure failure domain
- strict permissions and code review are required to prevent accidental cross-schema coupling
- very different workload profiles may eventually require separate databases

## Rejected Alternatives
### One PostgreSQL instance/database per service from day one
Rejected for MVP because it multiplies cost, backups, connections, monitoring and operational overhead without proven need.

### One shared schema for all services
Rejected because it encourages direct table coupling and undermines microservice ownership.

## Architecture Rule
**Shared PostgreSQL infrastructure is acceptable; shared table ownership is not.**

A Dev Agent must not introduce direct cross-service SQL access without a new reviewed ADR.

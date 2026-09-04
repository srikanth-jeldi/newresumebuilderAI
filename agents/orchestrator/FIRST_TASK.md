# ORCH-001 — Coordinate Resumaire Foundation

Status: READY_TO_RUN

## Objective
Coordinate the first real implementation cycle for the Resumaire foundation using Architect, Backend/Web, Mobile and QA agents.

## Preconditions
- Architect baseline/ADRs available on the working branch.
- Backend/Web Agent files exist.
- Mobile Agent files exist.
- QA Agent files exist.
- Architecture decisions required for foundation are sufficiently defined.

## Execution Plan

### Step 1 — Architecture Gate
Ask Architect to verify that the foundation is implementation-ready for:
- Java 21 / Spring Boot services
- Spring Cloud Gateway
- JWT access + rotating refresh token
- PostgreSQL schema isolation
- Docker Compose
- Next.js web foundation
- Flutter mobile foundation
- CI pipeline

If any cross-cutting decision is missing, keep the affected task blocked and route it to Architect.

### Step 2 — Start DEV1-001
Assign `agents/backend-web/FIRST_TASK.md` to Backend/Web Engineer.

Expected deliverables:
- service skeletons
- auth foundation
- PostgreSQL/Flyway foundation
- Docker Compose
- Next.js shell
- CI foundation
- completion report

### Step 3 — Start DEV2-001
When the auth/API foundation contract is stable enough, assign `agents/mobile/FIRST_TASK.md` to Mobile Engineer.

Expected deliverables:
- Flutter project foundation
- networking/auth client
- secure storage
- login/register/session restore
- dashboard/navigation shell
- tests
- completion report

DEV1 and DEV2 may work in parallel only after shared auth/API semantics are stable.

### Step 4 — Integration Build
Identify the exact integrated build/commit set to test.

Do not send QA an ambiguous moving target.

### Step 5 — Run QA-001
Assign `agents/qa/FIRST_TASK.md` to QA Engineer.

Required foundation checks include:
- registration
- login
- access token
- refresh rotation
- logout/revocation
- gateway route behavior
- cross-user authorization baseline
- PostgreSQL/Flyway startup
- Docker Compose startup
- web build/login/dashboard smoke
- Flutter analyze/test/login/dashboard smoke

### Step 6 — Failure Loop
For each QA defect:
- create FIX task
- route to Backend/Web or Mobile owner
- require developer validation
- set QA_RETEST
- QA verifies independently

### Step 7 — Foundation Release Gate
Foundation is READY only when:
- relevant CI checks pass
- QA foundation suite passes
- no BLOCKER/CRITICAL defects remain
- architecture deviations are resolved/documented
- exact commits/builds are known

## Final Report
Return:
- ARCHITECTURE STATUS
- DEV1 STATUS
- DEV2 STATUS
- QA STATUS
- CI STATUS
- OPEN DEFECTS
- SECURITY STATUS
- FOUNDATION RECOMMENDATION

Expected final recommendation:
`FOUNDATION_READY_FOR_FEATURE_DEVELOPMENT`
or
`FOUNDATION_NOT_READY`

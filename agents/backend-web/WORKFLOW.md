# Backend/Web Engineer — Execution Workflow

Follow this workflow for every assigned feature.

## Phase 1 — Intake
1. Read the assigned task.
2. Confirm the active project is Resumaire.
3. Read acceptance criteria.
4. Read all referenced architecture/ADR documents.
5. Identify affected backend service(s), web route(s), DB schema(s), external integrations and tests.
6. If a cross-service decision is missing, raise an architecture decision request instead of guessing.

## Phase 2 — Inspect Existing Implementation
1. Inspect the relevant service/module.
2. Inspect existing DTOs, controllers, services, repositories and tests.
3. Inspect Flyway migrations.
4. Inspect web components, routes, hooks/API clients, auth handling and design-system components.
5. Identify reusable code.
6. Identify overlapping active work before editing shared files.

## Phase 3 — Plan
Create a concise implementation plan covering:
- API changes
- authorization
- domain rules
- persistence/migration
- async jobs if needed
- web states
- tests
- rollback/compatibility risks

Prefer the smallest correct change.

## Phase 4 — Contract First
Before implementing a new cross-client endpoint:
1. Confirm/update the API contract.
2. Define request/response DTOs.
3. Define validation.
4. Define errors/status codes.
5. Define authorization and resource ownership.
6. Define async job contract if long-running.

Do not let web and mobile independently invent incompatible contracts.

## Phase 5 — Database
When persistence changes:
1. Modify only the owning service schema.
2. Create a versioned Flyway migration.
3. Add constraints/indexes justified by data rules/query patterns.
4. Avoid destructive migration unless explicitly approved.
5. Keep application changes compatible with the migration/deployment sequence.

## Phase 6 — Backend Implementation
Implement in this order where applicable:
1. DTOs/contracts
2. validation
3. authorization/ownership
4. application/domain logic
5. persistence adapters/repositories
6. external provider adapters
7. async job handling
8. controller/API
9. exception/error mapping
10. observability

## Phase 7 — Web Implementation
Implement according to screen contract:
1. route/page shell
2. API client integration
3. authenticated state
4. loading state
5. primary success flow
6. validation errors
7. empty state
8. dependency/network errors
9. retry/session expiry behavior
10. responsive layout
11. accessibility
12. field-level AI interactions where required

## Phase 8 — Tests
Backend:
- unit tests for business rules
- integration tests for DB/API
- authorization negative tests
- idempotency tests for critical job/payment/webhook flows

Web:
- component/integration tests where valuable
- critical Playwright flows
- responsive/manual visual review where automation is insufficient

## Phase 9 — Validation
Run relevant commands:
- backend Maven verify
- web lint/typecheck/test/build
- Docker image build
- migration validation

Then review the git diff for accidental changes.

## Phase 10 — Security Review
Before handoff verify:
- no secrets committed
- no private content logged
- ownership checks exist
- uploads are validated
- provider webhook signatures verified where relevant
- price/quota authority remains server-side
- AI cannot directly persist unsupported claims

## Phase 11 — Completion Report
Write the standard completion report and include exact QA focus areas.

## Phase 12 — Handoff
Status may become `DEV_COMPLETE` only when:
- acceptance criteria implemented
- relevant builds pass
- relevant tests pass
- architecture rules followed
- completion report exists

Then hand off to QA. A developer self-check is not QA approval.

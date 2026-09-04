# Architect Agent Workflow

Run these phases in order for every architecture task.

## Phase 1 — Discovery
1. Read the task and identify the project.
2. Read `docs/PRODUCT.md`.
3. Read current `docs/ARCHITECTURE.md`.
4. Read `docs/API_CONTRACTS.md`, `docs/DATA_MODEL.md`, and `docs/SECURITY.md`.
5. Inspect relevant source code when implementation exists.
6. Identify reusable modules, current constraints and unresolved decisions.
7. Do not redesign before discovery is complete.

## Phase 2 — Requirements
Create:
- business objective
- user stories
- functional requirements
- non-functional requirements
- assumptions
- constraints
- out-of-scope items

Requirements must be testable.

## Phase 3 — Architecture Impact
Determine impact on backend, web, mobile, database, auth, storage, AI, caching, async processing, observability and deployment. Add infrastructure only when justified.

## Phase 4 — API Contract
For each affected endpoint define method, path, auth, authorization, request, validation, response, errors, pagination/filter/sort and idempotency where relevant. Update `docs/API_CONTRACTS.md` before dependent clients build against it.

## Phase 5 — Data Design
Define entities, fields, relationships, constraints, indexes, ownership, lifecycle and migration impact. Update `docs/DATA_MODEL.md`.

## Phase 6 — Security Review
Verify authentication, authorization, object ownership, validation, secret handling, sensitive-data rules, file security and rate limits where relevant. Update `docs/SECURITY.md`.

## Phase 7 — Architecture Decision
Create an ADR for significant decisions. Do not create ADRs for trivial implementation details.

## Phase 8 — Task Breakdown
Create executable tasks. Every task needs owner, dependencies, objective, scope, out-of-scope, acceptance criteria, test requirements and prohibited changes.

Suggested IDs:
- ARCH-xxx
- DEV1-xxx (Web + Backend)
- DEV2-xxx (Flutter Mobile)
- QA-xxx

## Phase 9 — Dependency Graph
Define execution order. Independent tasks may run in parallel only after shared contracts are stable.

Example:
```text
ARCH-001
   |
API-001
  /   \
WEB-001 MOBILE-001
  \   /
  QA-001
```

## Phase 10 — Architecture Gate
Before READY confirm:
- requirements are implementable and testable
- architecture/module boundaries are clear
- API contract is defined
- data model is defined
- security/ownership rules are explicit
- tasks and dependencies are defined
- Definition of Done exists
- no major technical decision is left for developers to invent

Output `ARCHITECTURE_READY` or `ARCHITECTURE_BLOCKED`.

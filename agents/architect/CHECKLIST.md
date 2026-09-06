# Architect Readiness Checklist

## Discovery
- [ ] Product goal understood
- [ ] Existing docs read
- [ ] Existing implementation inspected when present
- [ ] Constraints and assumptions recorded

## Architecture
- [ ] System boundary defined
- [ ] Module boundaries defined
- [ ] Technology choices justified
- [ ] No premature microservices/queues/caching
- [ ] Failure modes considered

## API
- [ ] Shared API contracts documented
- [ ] Validation documented
- [ ] Error contract documented
- [ ] Pagination/filter/sort documented where relevant
- [ ] Idempotency considered for repeatable writes

## Data
- [ ] Entities and relationships documented
- [ ] Ownership documented
- [ ] Constraints documented
- [ ] Query-driven indexes considered
- [ ] Migration impact considered

## Security
- [ ] Authentication defined
- [ ] Authorization defined server-side
- [ ] Cross-user object access explicitly prevented
- [ ] Secret handling defined
- [ ] File access/privacy defined
- [ ] AI/privacy risks considered

## Operations
- [ ] Logging/metrics/tracing expectations defined
- [ ] Health/readiness expectations defined
- [ ] Deployment impact understood
- [ ] Rollback/mitigation considered for risky changes

## Handoff
- [ ] Dev1 tasks created
- [ ] Dev2 tasks created where mobile is affected
- [ ] QA focus areas created
- [ ] Dependencies defined
- [ ] Acceptance criteria are testable
- [ ] Definition of Done documented

Architecture may be marked `ARCHITECTURE_READY` only when all applicable items pass.

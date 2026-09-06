# Orchestrator Checklist

## Intake Gate
- [ ] Project identified
- [ ] Feature/bug objective clear
- [ ] User story clear
- [ ] Acceptance criteria testable
- [ ] Affected platforms/modules identified

## Architecture Gate
- [ ] Existing architecture reviewed
- [ ] Relevant ADRs reviewed
- [ ] Major shared decisions are defined
- [ ] API contract stable where multiple clients depend on it
- [ ] Data ownership defined
- [ ] Security/authorization defined
- [ ] Architecture status is READY or architecture is not required

## Dev Assignment Gate
- [ ] Correct owner selected
- [ ] Task scope is small enough to verify
- [ ] Dependencies are explicit
- [ ] Out-of-scope items stated
- [ ] Prohibited changes stated
- [ ] Test expectations stated
- [ ] Parallel work will not overwrite/conflict unexpectedly

## Developer Completion Gate
- [ ] Branch/commit identified
- [ ] Implementation summary supplied
- [ ] Files/modules changed supplied
- [ ] API changes documented
- [ ] DB/Flyway changes documented
- [ ] Config/env changes documented
- [ ] Build/lint/analyze result supplied
- [ ] Relevant tests pass
- [ ] Known limitations disclosed
- [ ] QA focus areas supplied

## QA Handoff Gate
- [ ] Exact build/commit supplied
- [ ] Acceptance criteria supplied
- [ ] Developer reports supplied
- [ ] Security ownership tests requested
- [ ] Regression scope identified
- [ ] Web/mobile/API scope identified

## QA Result Gate
- [ ] QA independently executed required tests
- [ ] Defects have severity and reproduction steps
- [ ] Fixes were re-tested by QA
- [ ] No developer self-approved the task

## Release Gate
- [ ] CI green
- [ ] QA passed or explicitly accepted low-risk exception
- [ ] No BLOCKER defects
- [ ] No CRITICAL defects
- [ ] HIGH defects resolved or consciously accepted
- [ ] Migration impact reviewed
- [ ] Rollback/mitigation understood
- [ ] Security gate passed
- [ ] Deployment artifact/image/commit is immutable and identifiable
- [ ] Human production approval required

## Post-Deploy Gate
- [ ] Services healthy
- [ ] Production smoke tests pass
- [ ] Database migration version confirmed
- [ ] Core auth/resume flow checked
- [ ] Rollback was not needed, or rollback status documented
- [ ] Status updated to RELEASED only after verification

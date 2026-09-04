# Orchestrator Workflow

## Phase 1 — Intake
1. Read the user/product request.
2. Identify project: Resumaire.
3. Classify work: feature / bug / architecture / release / maintenance.
4. Extract business objective and user story.
5. Identify affected areas: backend, web, mobile, data, AI, document pipeline, billing, security, CI/CD.
6. Reject vague execution and convert the request into testable acceptance criteria.

## Phase 2 — Architecture Check
1. Compare the request with current architecture docs and ADRs.
2. If it changes a major shared decision, create/route an Architect task.
3. Set status ARCHITECTURE_IN_PROGRESS.
4. Wait for ARCHITECTURE_READY or ARCHITECTURE_BLOCKED.
5. Do not allow Dev Agents to invent missing shared contracts.

## Phase 3 — Task Decomposition
Create task IDs by owner:
- ARCH-* for Architect
- DEV1-* for Backend/Web
- DEV2-* for Mobile
- QA-* for QA
- FIX-* for defects

Each task must include:
- project
- feature
- owner
- objective
- scope
- out of scope
- dependencies
- acceptance criteria
- API/data/security contract references
- files/modules likely affected
- prohibited changes
- validation required
- expected completion report

## Phase 4 — Dependency Graph
Create the execution order.

Typical flow:
ARCH
  ↓
Backend/API contract foundation
  ↓
+----------------+
|                |
Web             Mobile
|                |
+-------+--------+
        ↓
       QA

If web and mobile can work from a stable contract, allow parallel execution.

## Phase 5 — Dev Assignment
1. Set eligible tasks to DEV_READY.
2. Assign Backend/Web work to Dev Agent 1.
3. Assign Flutter work to Dev Agent 2.
4. Set status DEV_IN_PROGRESS once an agent begins.
5. Track blockers explicitly.
6. If blocker is architectural, route back to Architect instead of guessing.

## Phase 6 — Developer Completion Gate
Before QA, require the completion report.
Verify:
- correct branch/commit identified
- acceptance criteria addressed
- build/lint/analyze completed
- relevant tests completed
- migrations/config changes documented
- no unrelated changes hidden in scope
- known limitations stated

If incomplete, return task to developer.
If complete, set DEV_COMPLETE then QA_READY.

## Phase 7 — QA Handoff
Provide QA:
- exact requirement
- acceptance criteria
- exact build/commit
- developer completion reports
- API contracts
- affected platforms
- security checks required
- regression areas
- known risks

Set QA_IN_PROGRESS.

## Phase 8 — QA Result
### PASS
Set QA_PASSED and evaluate release gate.

### FAIL
1. Set QA_FAILED.
2. Create one or more defect tasks.
3. Route each defect to the owning developer.
4. Set those defects DEV_FIX.
5. Require developer reproduction and fix.
6. After developer validation, set QA_RETEST.
7. QA independently re-tests.
8. Repeat until passed or explicitly blocked/cancelled.

## Phase 9 — Release Gate
Verify:
- CI green
- QA passed
- security gate passed
- DB migration impact understood
- rollback/mitigation known
- no BLOCKER/CRITICAL bugs
- accepted risks documented

If yes: RELEASE_READY.
If no: NOT_READY / return to relevant owner.

Production deployment is never automatic merely because status is RELEASE_READY. Human approval is required.

## Phase 10 — Release Completion
After approved deployment:
- confirm production health
- run smoke tests
- record deployed image/commit versions
- record migration version
- verify core user flow
- mark RELEASED only after verification

## Resumaire Feature Example
Feature: Optimize uploaded resume for a job description.

1. Architect verifies upload, tailoring and document contracts.
2. Dev1 implements backend APIs + web flow.
3. Dev2 implements mobile flow from same APIs.
4. QA tests:
   - upload
   - ownership
   - parse states
   - project selection
   - AI factual integrity
   - accept/reject flow
   - ATS score
   - DOCX/PDF export
   - regression
5. Failures route to correct developer.
6. QA re-tests fixes.
7. Orchestrator marks RELEASE_READY only after all gates pass.

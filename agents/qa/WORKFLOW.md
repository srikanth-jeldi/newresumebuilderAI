# QA Agent Workflow

## Phase 1 — Intake
1. Read requirement and acceptance criteria.
2. Read developer completion report.
3. Identify exact branch/commit/build under test.
4. Read affected API contracts, architecture and UX flow.
5. Identify risk areas and regression scope.

## Phase 2 — Test Design
Create a test matrix covering:
- happy path
- invalid input
- boundary values
- authentication/session expiry
- authorization/ownership
- network failure/retry
- duplicate requests/idempotency
- responsive/mobile behavior
- document fidelity
- AI safety/contract behavior
- quota/billing behavior where applicable

## Phase 3 — API Verification
Validate:
- HTTP status codes
- request validation
- structured error envelope
- auth required where expected
- ownership checks
- pagination/filter/sort where applicable
- async job states and retry behavior
- no sensitive data leaks

## Phase 4 — Web Verification
Test:
- Chrome-class desktop flow
- responsive tablet/mobile widths
- form validation
- loading/empty/error states
- deep links/refresh where relevant
- keyboard/focus basics
- no horizontal overflow
- correct CTA enable/disable rules

## Phase 5 — Mobile Verification
Test representative:
- small phone
- standard phone
- large phone
- tablet where supported

Validate:
- navigation/back behavior
- keyboard handling
- session restore
- secure-token behavior
- slow network
- app resume
- downloads/share
- permission denial where applicable

## Phase 6 — Document Workflow
For representative DOCX fixtures verify:
- source is unchanged
- edits target intended bullets only
- styles/bullets/tables/page breaks survive
- generated DOCX opens successfully
- generated PDF renders all content
- unsupported constructs surface warning rather than corruption

For PDF input verify best-effort parsing and that exact-fidelity claims are not made.

## Phase 7 — AI Workflow
Verify:
- structured output schema
- supported rewrite vs requires-confirmation classification
- unsupported JD experience not auto-added
- accepted/rejected states respected
- provider failure does not lose user work
- retry does not double-charge quota

## Phase 8 — Defect Reporting
Every defect must include:
TITLE
SEVERITY
PROJECT/FEATURE
BUILD/COMMIT
PLATFORM
ENVIRONMENT
PRECONDITIONS
STEPS
EXPECTED
ACTUAL
EVIDENCE
REGRESSION?
AFFECTED FLOW

Severity:
- BLOCKER
- CRITICAL
- HIGH
- MEDIUM
- LOW

## Phase 9 — Re-test
1. Developer reports fix.
2. Test exact original reproduction steps.
3. Test nearby regression scope.
4. Mark VERIFIED only if defect is actually gone.

## Phase 10 — Release Recommendation
Return:
- test count executed/passed/failed/blocked
- unresolved defects
- security observations
- document fidelity result
- regression result
- final PASS/PASS_WITH_KNOWN_LOW_RISK_ISSUES/FAIL

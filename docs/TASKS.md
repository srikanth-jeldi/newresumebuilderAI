# Architecture and Delivery Task Model

## Canonical Status Flow
```text
NEW
 -> ARCHITECTURE_IN_PROGRESS
 -> ARCHITECTURE_READY
 -> DEV_READY
 -> DEV_IN_PROGRESS
 -> DEV_COMPLETE
 -> QA_READY
 -> QA_IN_PROGRESS
 -> QA_FAILED -> DEV_FIX -> QA_RETEST
 -> QA_PASSED
 -> RELEASE_READY
 -> RELEASED
```

Blocked work may use `BLOCKED` with a documented blocker and owner.

## Task Template
```text
ID:
PROJECT:
FEATURE:
OWNER:
STATUS:

BUSINESS OBJECTIVE:
USER STORY:

SCOPE:
OUT OF SCOPE:
DEPENDENCIES:

ARCHITECTURE CONTEXT:
API CONTRACT:
DATA IMPACT:
SECURITY REQUIREMENTS:

ACCEPTANCE CRITERIA:
1.
2.
3.

TEST REQUIREMENTS:
DO NOT CHANGE:
EXPECTED OUTPUT:
```

## First Architecture Task
### ARCH-001 — Resume Builder MVP Architecture
Owner: Architect Agent
Status: NEW

Objective: turn the current architecture baseline into an implementation-ready MVP design for web, Flutter Android/iOS, backend and AI capabilities.

Required output:
- finalize backend/framework and DB decisions
- finalize auth model
- define detailed core Resume API/data contracts
- define PDF export approach
- define AI provider abstraction and ATS analysis boundary
- define hosting/storage direction
- create Dev1/Dev2/QA implementation backlog with dependencies
- update relevant ADRs

Completion status must be `ARCHITECTURE_READY` or `ARCHITECTURE_BLOCKED`.

# Orchestrator Task Schema

Use this structure for every coordinated task.

```yaml
id: DEV1-001
project: Resumaire
feature: Foundation
owner: Backend/Web Engineer
status: DEV_READY
priority: HIGH

objective: >
  Build the approved backend/web foundation for Resumaire.

scope:
  - ...

out_of_scope:
  - ...

depends_on:
  - ARCH-001

architecture_refs:
  - docs/ARCHITECTURE.md
  - docs/API_CONTRACTS.md
  - docs/SECURITY.md
  - docs/ADR/...

acceptance_criteria:
  - ...

security_requirements:
  - ...

validation_required:
  - build
  - tests
  - lint/analyze

prohibited_changes:
  - do not change agreed service boundaries
  - do not weaken authentication/authorization

completion_report_required: true
```

## Defect Task Schema

```yaml
id: FIX-001
project: Resumaire
feature: Authentication
owner: Backend/Web Engineer
status: DEV_FIX
source_qa_task: QA-001
severity: HIGH

bug:
  title: Refresh token reuse is not rejected
  environment: staging
  build_commit: <sha>
  preconditions:
    - authenticated user exists
  steps_to_reproduce:
    - ...
  expected: ...
  actual: ...

acceptance_criteria:
  - original defect no longer reproduces
  - nearby auth regression passes

next_status_after_dev_validation: QA_RETEST
```

## Completion Report Schema

```yaml
project: Resumaire
feature: ...
owner: ...
branch: ...
commit: ...

implemented:
  - ...

files_changed:
  - ...

api_changes:
  - ...

database_changes:
  - ...

config_changes:
  - ...

tests_added:
  - ...

commands_run:
  - ...

results:
  - ...

known_limitations:
  - ...

qa_focus:
  - ...
```

## Orchestrator Status Transition Rules

```text
NEW
→ ARCHITECTURE_IN_PROGRESS (when architecture needed)
→ ARCHITECTURE_READY
→ DEV_READY
→ DEV_IN_PROGRESS
→ DEV_COMPLETE
→ QA_READY
→ QA_IN_PROGRESS
→ QA_PASSED
→ RELEASE_READY
→ RELEASED
```

Failure paths:

```text
ARCHITECTURE_IN_PROGRESS → ARCHITECTURE_BLOCKED
DEV_IN_PROGRESS → DEV_BLOCKED
QA_IN_PROGRESS → QA_FAILED → DEV_FIX → QA_RETEST → QA_IN_PROGRESS
Any eligible task → CANCELLED when product owner intentionally cancels it
```

Do not skip states merely to make progress appear faster.

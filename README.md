# New Resume Builder AI

AI-assisted resume builder for web, Android and iOS.

## Engineering Approach
This project is being built with a controlled agent-driven engineering workflow:

```text
Product Owner
   -> Architect Agent
   -> Dev Agent 1 (Web + Backend)
   -> Dev Agent 2 (Flutter Mobile)
   -> Independent QA Agent
   -> Release Gate / Human Approval
```

Agents do not receive unrestricted production authority. Architecture contracts, feature branches, automated checks, independent QA and human release approval are core controls.

## Current Stage
`ARCH-001` — establish implementation-ready MVP architecture.

## Repository Documentation
- `docs/PRODUCT.md` — product scope and MVP outcomes
- `docs/ARCHITECTURE.md` — architecture baseline
- `docs/API_CONTRACTS.md` — shared API contract map
- `docs/DATA_MODEL.md` — data model baseline
- `docs/SECURITY.md` — security/privacy baseline
- `docs/TASKS.md` — status model and initial architecture task
- `docs/ADR/` — architecture decision records
- `agents/architect/` — Architect Agent role, workflow and quality gate

## Next Gate
The Architect Agent must complete `ARCH-001` before development agents receive implementation work.

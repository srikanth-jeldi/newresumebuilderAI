# Resumaire — GitHub Issues + n8n Automation

## Goal
Use GitHub Issues as the durable task queue/state record and n8n as the event-driven orchestrator that routes work between the Resumaire agents.

The automation must never directly merge to protected production branches or deploy production without a human approval gate.

## Source of Truth
GitHub Issues are the source of truth for task state.

Required labels:
- `agent:orchestrator`
- `agent:architect`
- `agent:backend-web`
- `agent:mobile`
- `agent:qa`
- `status:new`
- `status:in-progress`
- `status:blocked`
- `status:ready-for-dev`
- `status:dev-complete`
- `status:ready-for-qa`
- `status:qa-failed`
- `status:qa-passed`
- `status:release-ready`
- `status:done`

## Workflow 1 — New Task Intake
Trigger: GitHub issue opened/labeled.

Condition:
- repository = `srikanth-jeldi/newresumebuilderAI`
- issue has `status:new`

Actions:
1. Read issue body.
2. Determine owning agent from `agent:*` label.
3. Load the corresponding agent instruction files from the repository.
4. Load relevant product/architecture documents.
5. Invoke the configured AI model with the issue + agent instructions + relevant docs.
6. Post a structured execution/handoff comment on the issue.
7. Replace `status:new` with `status:in-progress` when execution has actually started.
8. Never mark a task complete from prose alone; require the completion evidence defined by the agent contract.

## Workflow 2 — Developer Completion
Trigger: issue comment or label transition indicating a developer completion report.

Actions:
1. Validate that build/test evidence is present.
2. If both required development dependencies are complete, apply `status:ready-for-qa` to the QA task or create the QA handoff issue.
3. Include exact branch/commit/build identifiers in the QA handoff.

## Workflow 3 — QA Failure Loop
Trigger: QA task receives `status:qa-failed`.

Actions:
1. Parse defect owner from QA report.
2. Create or update a FIX issue for the correct developer agent.
3. Link the original QA issue and failing build/commit.
4. After developer completion, move the QA issue to re-test.
5. QA is the only role that can provide the final verification result for the defect loop.

## Workflow 4 — Release Ready
Trigger: QA task receives `status:qa-passed`.

Checks:
- required CI checks passed
- no BLOCKER/CRITICAL defects open
- required HIGH defects resolved or explicitly accepted
- migrations documented
- target build/commit immutable and identified

Action:
- mark coordinating task `status:release-ready`
- post release summary
- stop automation before production merge/deploy unless a human explicitly approves the next action

## Credentials Required in n8n
Create credentials in n8n UI; never place secrets in repository files:
- GitHub credential/token or GitHub App connection with repository issue/comment/read permissions and only the write scopes actually required
- AI provider credential (OpenAI primary; optional Gemini fallback)

## Recommended n8n Variables
- `RESUMAIRE_REPO=srikanth-jeldi/newresumebuilderAI`
- `RESUMAIRE_DEFAULT_BRANCH=main`
- `RESUMAIRE_AGENT_BRANCH=setup/architect-agent` until PR #1 is merged
- `RESUMAIRE_HUMAN_APPROVAL_REQUIRED=true`

## Polling vs Webhook
Prefer GitHub-triggered/webhook execution. Do not poll GitHub every few minutes when event triggers are available.

## Idempotency
Every workflow must be safe to receive the same GitHub event more than once.
Use a key based on:
- repository
- issue number
- GitHub event/delivery identifier when available
- target state transition

Before posting duplicate comments or creating duplicate child issues, search for an existing automation marker.

Recommended hidden marker inside automation comments:
`<!-- resumaire-automation:<workflow>:<task-id>:<transition> -->`

## Failure Handling
If AI invocation, GitHub write, or dependency lookup fails:
- do not advance the issue state
- add/record a machine-readable failure reason when possible
- retry only safe/idempotent steps
- after bounded retries, set `status:blocked` and surface the reason

## Human-Controlled Operations
n8n must not automatically:
- merge protected production PRs
- deploy production
- run destructive DB migrations
- rotate production secrets
- publish Android/iOS store releases
- delete production/user data

## First Live Test
Use GitHub issue `ORCH-001 — Coordinate Resumaire foundation`.
Expected first transition:
`agent:orchestrator + status:new` → Orchestrator reads architecture/task files → posts plan → `status:in-progress`.

# n8n Setup — Resumaire Agent Orchestration

## 1. Deploy or Open n8n
Use either n8n Cloud or a self-hosted n8n instance. For a budget-first startup, self-hosting on the same non-production VPS is acceptable initially if resources allow; production orchestration should later be isolated if reliability/security requires it.

## 2. Create GitHub Credential
In n8n create a GitHub credential for `srikanth-jeldi/newresumebuilderAI`.

Minimum practical capabilities:
- read repository contents
- read issues/PRs
- create/update issue comments and labels
- create issues for child tasks/defects

Avoid broad organization/admin permissions.

## 3. Create AI Credential
Configure the primary OpenAI credential in n8n. Optional: add Gemini as a fallback after the primary workflow is proven.

Do not paste API keys into workflow JSON, issue text, or repository files.

## 4. Build Workflow: Resumaire — New Task Router
Create these nodes in order:

### Node A — GitHub Trigger
Events:
- Issue opened
- Issue labeled

Repository:
- owner: `srikanth-jeldi`
- repository: `newresumebuilderAI`

### Node B — Filter / IF
Continue only if:
- issue is open
- labels include `status:new`
- at least one `agent:*` label exists

### Node C — Normalize Task
Use a Set/Code node to extract:
- repository
- issue number
- issue URL
- title
- body
- owner-agent label
- status label
- delivery/event identifier when available

### Node D — Idempotency Check
Search issue comments for a marker matching:
`<!-- resumaire-automation:new-task:<issue-number>:started -->`

If found, stop. This prevents duplicate delivery from triggering the task twice.

### Node E — Fetch Agent SYSTEM.md
Map owner label to file:
- `agent:orchestrator` → `agents/orchestrator/SYSTEM.md`
- `agent:architect` → `agents/architect/SYSTEM.md`
- `agent:backend-web` → `agents/backend-web/SYSTEM.md`
- `agent:mobile` → `agents/mobile/SYSTEM.md`
- `agent:qa` → `agents/qa/SYSTEM.md`

Read from `setup/architect-agent` until PR #1 is merged; then switch to `main`.

### Node F — Fetch Agent WORKFLOW.md and CHECKLIST.md
Use the same role mapping.

### Node G — Fetch Shared Context
Always fetch:
- `docs/PRODUCT.md`
- `docs/ARCHITECTURE.md`
- `docs/SECURITY.md`
- `docs/API_CONTRACTS.md`
- `docs/UX_FLOW.md`

For implementation roles also load the task-specific `FIRST_TASK.md` when relevant.

Do not send the entire repository to the model. Only retrieve files relevant to the task.

### Node H — AI Agent Invocation
Construct prompt sections in this order:
1. Agent system instructions
2. Agent workflow
3. Agent checklist
4. Product/architecture constraints
5. GitHub issue/task
6. Required output schema

Require structured output with fields:
- `taskId`
- `agent`
- `decision`
- `plan`
- `dependencies`
- `risks`
- `requiredActions`
- `nextStatus`
- `handoff`

Never allow the model output itself to perform production actions.

### Node I — Validate Output
Reject model response when:
- required fields missing
- `nextStatus` is not in allowed state list
- owner conflicts with issue label
- output asks for prohibited production operation

### Node J — Post GitHub Comment
Post the structured plan and hidden marker:
`<!-- resumaire-automation:new-task:<issue-number>:started -->`

### Node K — Update Labels
Remove `status:new`.
Add `status:in-progress`.

Do this only after the plan comment succeeds.

## 5. Build Workflow: Developer Completion Router
Trigger on issue comments and/or label changes.

Recognize a completion report only when it contains the standard completion fields defined in the agent instructions.

If Dev1/Dev2 dependencies are complete:
- create/find the linked QA issue
- include exact PR/branch/commit/build identifiers
- set QA issue to `status:ready-for-qa`

## 6. Build Workflow: QA Failure Router
Trigger when `status:qa-failed` appears.

Parse each defect and route by ownership:
- backend/API/web → `agent:backend-web`
- Android/iOS/Flutter → `agent:mobile`
- architecture/contract ambiguity → `agent:architect`

Create FIX issue only when one does not already exist.
Link:
- failing QA issue
- build/commit
- reproduction steps
- severity

## 7. Build Workflow: QA Pass → Release Gate
When QA passes:
- verify CI result reference exists
- check unresolved critical defects
- post release summary
- mark parent `status:release-ready`

Stop. Human approval remains required for production.

## 8. Initial Test
Use issue #2 (`ORCH-001`).

Expected result:
1. GitHub Trigger receives issue event or manually re-label issue with `status:new` after workflow activation.
2. Orchestrator instructions/context are fetched.
3. AI generates first coordination plan.
4. n8n comments on issue #2.
5. `status:new` becomes `status:in-progress`.

## 9. Safety Test Before Enabling Full Automation
Intentionally trigger the same event twice.
Expected:
- only one orchestration comment
- only one state transition
- no duplicate child issues

## 10. Credential Rotation
If any GitHub/AI credential is accidentally pasted into an issue, repo, log, or exported workflow, revoke it immediately and create a new credential.

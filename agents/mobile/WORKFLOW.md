# Mobile Engineer Workflow

Follow this workflow for every Flutter task.

## Phase 1 — Read the Task
1. Confirm project = Resumaire.
2. Read task objective and acceptance criteria.
3. Confirm affected platform(s): Android, iOS, tablet, all mobile.
4. Confirm backend/API dependencies.
5. Identify whether the required API contract already exists.

If the contract is missing or contradictory, stop and return to Architect/Backend-Web owner.

## Phase 2 — Inspect Existing Mobile Code
1. Inspect router/navigation.
2. Inspect state-management pattern.
3. Inspect theme/design system.
4. Inspect API client and auth interceptors.
5. Inspect shared widgets/components.
6. Inspect existing tests.
7. Identify the smallest safe implementation path.

Do not rewrite working infrastructure merely because another pattern is preferred.

## Phase 3 — Define UX States
For every new screen define:
- entry condition
- required inputs
- primary CTA
- CTA disabled rules
- loading state
- success state
- empty state
- validation error
- network error
- unauthorized/session-expired state
- quota/rate-limit state where relevant
- retry behavior
- next screen
- back/cancel behavior
- phone/tablet behavior

Long-running processing additionally requires:
- QUEUED
- RUNNING
- SUCCEEDED
- FAILED
- CANCELLED if supported

## Phase 4 — Implement Data Layer
1. Add/update typed API models.
2. Add repository/client call.
3. Use versioned gateway endpoints.
4. Map backend error envelope into application errors.
5. Add timeout behavior.
6. Add safe auth-refresh behavior.
7. Do not add unsafe automatic retries for non-idempotent operations.

## Phase 5 — Implement State Logic
1. Keep API/state logic outside presentation widgets where practical.
2. Make state transitions explicit.
3. Prevent duplicate submit actions.
4. Preserve unsaved user input.
5. Handle refresh/session expiry.
6. Avoid stale async results overwriting newer state.

## Phase 6 — Implement UI
1. Reuse theme tokens and shared widgets.
2. Build phone layout first where task is phone-centric.
3. Verify larger phones and tablet behavior.
4. Respect safe areas.
5. Check keyboard behavior.
6. Keep key CTAs reachable.
7. Use mobile-native layouts rather than desktop compression.
8. Include accessibility semantics where needed.

## Phase 7 — AI-Specific UI Rules
Whenever AI generates content:
1. Show processing state.
2. Show proposal/result clearly.
3. Never silently overwrite the source text.
4. Let the user Accept/Reject/Edit where product flow requires it.
5. Clearly distinguish unsupported JD skills from real resume experience.
6. Surface quota/rate-limit failures clearly.
7. Preserve the user's current work if AI provider fails.

## Phase 8 — File Workflow Rules
For resume/JD upload/download:
1. Enforce supported extension/size rules in UI before upload where possible.
2. Still rely on backend validation as authority.
3. Show upload progress where useful.
4. Handle password-protected/unsupported/parse-failure states.
5. Never expose permanent public private-file URLs.
6. Use short-lived authenticated download/share flow.

## Phase 9 — Test
At minimum run:

```text
flutter format .
flutter analyze
flutter test
```

When relevant also:
- test Android emulator/device
- test iOS simulator/device on macOS
- test small phone
- test large phone
- test tablet
- test slow/offline network
- test expired auth
- test keyboard/forms
- test deep navigation/back flow

## Phase 10 — Review Diff
Before completion:
1. Inspect changed files.
2. Remove debug logs.
3. Remove temporary flags.
4. Remove fake production data.
5. Confirm no secrets were added.
6. Confirm no unrelated backend/web changes.

## Phase 11 — Completion Report
Return:
- summary
- screens changed
- files changed
- state-management changes
- APIs consumed
- platform configuration changes
- tests
- build/analyze results
- limitations
- QA focus

Only then set task status to `DEV_COMPLETE` or equivalent agreed state.
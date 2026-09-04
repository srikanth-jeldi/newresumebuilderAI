# Mobile Engineer Readiness Checklist

Use this checklist before marking a Flutter task complete.

## Architecture
- [ ] Read current architecture docs and relevant ADRs.
- [ ] Used existing API contracts.
- [ ] Did not invent a conflicting backend contract.
- [ ] Did not introduce an unapproved state-management framework.
- [ ] Did not modify unrelated backend/web architecture.

## Authentication
- [ ] Tokens use secure storage.
- [ ] Access-token expiry is handled.
- [ ] Refresh failure returns user to login safely.
- [ ] No infinite refresh loop.
- [ ] Logout clears sensitive local session data.
- [ ] No password/token logging.

## UI / UX
- [ ] Entry condition defined.
- [ ] Primary CTA defined.
- [ ] Loading state implemented.
- [ ] Empty state implemented where needed.
- [ ] Validation errors implemented.
- [ ] Network failure/retry implemented.
- [ ] Unauthorized/session-expired state implemented.
- [ ] Back/cancel behavior checked.
- [ ] Small phone checked.
- [ ] Large phone checked.
- [ ] Tablet behavior considered/tested.
- [ ] Keyboard does not block critical actions.
- [ ] Safe areas respected.
- [ ] Accessibility labels/semantics added where appropriate.

## Resume / AI
- [ ] Source resume is never silently overwritten.
- [ ] AI proposals are visible to the user.
- [ ] Accept/Reject/Edit works where required.
- [ ] Unsupported JD experience is not shown as confirmed experience.
- [ ] AI failure does not destroy user work.
- [ ] Quota/usage-limit errors are handled.

## Documents
- [ ] Upload type/size UX validation implemented where applicable.
- [ ] Backend remains validation authority.
- [ ] Upload/parsing states handled.
- [ ] Password-protected/unsupported errors handled where applicable.
- [ ] Private files do not use permanent public URLs.
- [ ] Download/share failures handled.

## Async Jobs
- [ ] QUEUED handled.
- [ ] RUNNING handled.
- [ ] SUCCEEDED handled.
- [ ] FAILED handled.
- [ ] Duplicate submit prevented.
- [ ] Polling stops when terminal state is reached.
- [ ] Polling handles app background/resume safely.

## Performance
- [ ] No obvious unnecessary rebuild loops.
- [ ] Large lists paginate/lazy load where required.
- [ ] Images/heavy previews load efficiently.
- [ ] Stale requests cannot overwrite current screen state.

## Testing
- [ ] `flutter format .` completed.
- [ ] `flutter analyze` completed with no unresolved relevant errors.
- [ ] `flutter test` completed for relevant scope.
- [ ] Critical happy path manually validated.
- [ ] Failure path manually validated.
- [ ] Auth expiry tested where relevant.
- [ ] Android checked when task affects Android.
- [ ] iOS checked when environment/task permits and iOS is affected.

## Git / Release Safety
- [ ] Changes are scoped to assigned task.
- [ ] No secrets committed.
- [ ] No debug credentials committed.
- [ ] No direct protected-main push.
- [ ] No store release performed without approved release workflow.

## Completion Report
- [ ] Feature summary provided.
- [ ] Screens listed.
- [ ] Files listed.
- [ ] APIs listed.
- [ ] Tests/results listed.
- [ ] Known limitations listed.
- [ ] QA focus areas listed.

If any critical item is unchecked, the task is not complete.
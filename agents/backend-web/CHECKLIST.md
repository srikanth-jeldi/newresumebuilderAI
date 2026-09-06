# Backend/Web Engineer — Definition of Done Checklist

A feature may not be marked `DEV_COMPLETE` until all applicable checks pass.

## Architecture
- [ ] Correct service owns the change.
- [ ] No unauthorized cross-schema access.
- [ ] Existing ADRs followed.
- [ ] No unnecessary new infrastructure/framework.

## API
- [ ] Request/response contract defined.
- [ ] Validation implemented.
- [ ] Consistent error envelope used.
- [ ] Authentication enforced.
- [ ] Resource ownership/authorization enforced.
- [ ] Pagination/filtering/sorting added where needed.
- [ ] Long-running work uses async job pattern where applicable.

## Database
- [ ] Flyway migration added for schema changes.
- [ ] Constraints/indexes justified.
- [ ] No cross-service foreign keys.
- [ ] No production Hibernate auto-update dependency.

## Security
- [ ] No secrets in code/logs.
- [ ] User A cannot access User B resources.
- [ ] Uploaded files validated.
- [ ] Private files remain private.
- [ ] Webhooks verified and idempotent where relevant.
- [ ] AI output validated before persistence/use.

## Resume/Document Integrity
- [ ] Original source resume remains immutable.
- [ ] Derived version lineage maintained.
- [ ] AI changes visible/reviewable.
- [ ] Unsupported experience is not fabricated.
- [ ] DOCX fidelity path preserved where applicable.

## Web UX
- [ ] Loading state.
- [ ] Success state.
- [ ] Empty state.
- [ ] Validation error state.
- [ ] Network/dependency error state.
- [ ] Session-expired state.
- [ ] Retry behavior.
- [ ] Desktop/tablet/mobile responsive review.
- [ ] Accessibility basics checked.
- [ ] AI field actions never silently overwrite user text.

## Billing/Usage
- [ ] Plan/price/quota comes from backend config.
- [ ] Quota reservation/finalization is idempotent where applicable.
- [ ] Frontend payment callback alone cannot grant paid access.
- [ ] Provider-owned checkout used for sensitive payment entry.

## Tests
- [ ] Unit tests pass.
- [ ] Integration/API tests pass.
- [ ] Authorization negative tests pass.
- [ ] Web tests pass where applicable.
- [ ] Critical E2E flow covered or explicitly documented for QA.

## Build
- [ ] Maven verify passes for affected backend.
- [ ] Web lint/typecheck/build passes.
- [ ] Docker build passes for affected deployable component.
- [ ] No unexplained critical warnings.

## Git/Review
- [ ] Diff contains only intended changes.
- [ ] No debug code/secrets/temp files.
- [ ] Completion report written.
- [ ] QA focus areas documented.

Final developer status:
- `DEV_COMPLETE`
- or `DEV_BLOCKED` with an explicit blocker.

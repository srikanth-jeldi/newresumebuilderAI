# QA Release Checklist

A candidate build may be recommended for release only when applicable checks pass.

## Build / Environment
- Exact commit/build identified
- Required services healthy
- Database migrations applied
- Object storage reachable
- Document worker reachable
- Test data prepared

## Authentication
- Register
- Login
- Refresh
- Logout
- Expired token
- Revoked session
- Invalid credentials

## Authorization
- Cross-user resume access denied
- Cross-user version access denied
- Cross-user export access denied
- Cross-user ATS access denied
- Cross-user cover-letter access denied
- Cross-user interview-session access denied
- Cross-user application access denied

## Resume Flow
- Create/upload resume
- Parse
- Detect sections/projects
- Project selection
- Build Resume
- Customize Resume
- Accept/reject/edit AI proposals
- ATS analysis
- Preview
- DOCX export
- PDF export
- Version history

## AI Integrity
- No silent overwrite
- No unsupported experience fabrication
- Requires-confirmation path works
- Structured output validated
- Provider failure graceful
- Retry/idempotency correct

## Web
- Desktop
- Tablet
- Mobile responsive
- Form errors
- Loading states
- Empty states
- Retry states
- Keyboard/focus basics

## Mobile
- Android
- iOS where build/device available
- Session restore
- Back navigation
- Keyboard
- Slow network
- Download/share
- App resume

## Documents
- Representative DOCX fidelity suite
- Source immutable
- Generated DOCX opens
- PDF complete/readable
- Complex layout warning behavior

## Billing / Quotas
- FREE limits enforced
- PRO entitlement activation only after authoritative payment state
- Failed operations not double-counted
- Duplicate requests/webhooks idempotent
- Downgrade/expired behavior safe

## Regression
- Auth smoke
- Resume smoke
- Export smoke
- AI smoke
- Interview prep smoke
- Application tracker smoke

## Final Gate
FAIL if:
- BLOCKER open
- CRITICAL open
- unresolved unauthorized private-data access
- source resume corruption
- export loses approved content
- payment entitlement can be forged client-side

Record final status with evidence.

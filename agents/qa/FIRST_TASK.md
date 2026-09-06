# QA-001 — Foundation Verification

## Owner
QA / Test Engineer Agent

## Objective
Independently verify the first backend/web/mobile foundation once DEV1-001 and DEV2-001 provide completion reports and exact build/commit references.

## Dependencies
- DEV1-001 complete
- DEV2-001 complete where mobile validation is in scope
- required local/staging environment available

## Test Scope
### Backend/API
- gateway reachable
- register/login/refresh/logout work through gateway
- invalid credentials rejected
- refresh-token rotation works
- revoked/expired session rejected
- Flyway schemas exist
- service health endpoints work
- error envelope consistent

### Security
Create User A and User B.
When resume endpoints become available, verify User B cannot read/change User A resources by ID.
For foundation auth, verify protected endpoints cannot be accessed anonymously.

### Web
- Resumaire shell loads
- Login/Register screens work
- Dashboard requires authentication
- logout returns to unauthenticated state
- mobile/tablet responsive shell has no obvious overflow

### Mobile
- Flutter app launches
- Login/Register works against gateway
- tokens stored using secure storage approach
- session restoration works
- logout clears usable session
- Android build/test passes
- iOS compile/build check when macOS/Xcode environment is available

### Infrastructure
- Docker Compose starts required local services
- PostgreSQL reachable
- MinIO reachable
- no secrets committed into source/config examples

### CI
Verify configured PR CI executes relevant backend/web/mobile checks and fails appropriately on a deliberately failing test only in a safe test branch when such validation is requested.

## Required Output
- Build/commit tested
- Environment
- Test cases executed
- Passed/failed/blocked counts
- Defects with severity and reproduction
- Security observations
- Regression observations
- Final PASS / PASS_WITH_KNOWN_LOW_RISK_ISSUES / FAIL

## Important
Do not mark PASS if core acceptance tests were not executable. Mark blocked scope explicitly.

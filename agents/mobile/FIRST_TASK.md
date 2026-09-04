# DEV2-001 — Flutter Mobile Foundation

Owner: Mobile Engineer Agent

Status: DEV_READY after required backend auth/gateway contracts are stable enough for integration.

## Objective
Create the production-ready Flutter foundation for Resumaire Android and iOS applications, including architecture, navigation, theme, authentication shell, secure session handling, dashboard shell and API integration scaffolding.

This task is intentionally a foundation slice. Do not attempt to build every Resumaire feature in one task.

## Dependencies
Required architecture/contracts:
- Architect decisions accepted
- Gateway/auth API contract defined
- login/register/refresh/logout contract stable enough for client integration

If backend implementation is not yet runnable, use contract-based adapters/mocks only where explicitly allowed and keep them isolated from production networking code.

## Scope

### 1. Flutter Application Bootstrap
Create the Flutter application under the agreed repository location, for example:

```text
mobile/
```

Support:
- Android
- iOS

Establish:
- app entry point
- environment configuration
- routing
- theme/design tokens
- localization-ready structure if simple to support without unnecessary complexity
- core error model
- networking module
- secure storage wrapper

### 2. Feature-Oriented Structure
Create a clean structure for at least:

```text
lib/
  app/
  core/
  features/
    auth/
    dashboard/
    resumes/
    templates/
    tailoring/
    ats/
    interview_prep/
    applications/
```

Do not generate empty boilerplate hundreds of files deep. Create only useful boundaries needed for the first implementation slices.

### 3. State Management
Select one consistent state-management approach appropriate for production Flutter and document the choice.

Requirements:
- testable
- clear async states
- suitable for auth/session state
- suitable for multi-step workflows
- does not require mixing several state libraries

### 4. Networking Foundation
Implement/configure:
- environment-specific gateway base URL
- typed API request/response handling
- backend error-envelope decoding
- auth access-token injection
- timeout handling
- refresh-token coordination
- request retry only where safe

Prevent simultaneous expired requests from starting uncontrolled parallel token refresh operations.

### 5. Secure Session Foundation
Implement secure storage abstraction for:
- refresh/session material as required by approved auth flow
- access token storage strategy as approved for the client

Required behavior:
- session restore on app start
- refresh when needed
- logout cleanup
- expired/invalid refresh returns to login

Do not store passwords.

### 6. Splash / Session Check
Create initial session-check experience:

```text
App Start
↓
Check stored session
├── valid/refreshable → Dashboard
└── invalid/missing → Login
```

Avoid a blank white screen during meaningful startup work.

### 7. Authentication UI
Implement mobile-native shells/screens for:
- Login
- Register
- Forgot Password placeholder/navigation only if backend contract is not yet implemented

Use Resumaire branding:
- Resumaire
- An EpitomeHub® Product

Do not add unverified social-login buttons unless the flow is actually supported or they are clearly disabled/coming later.

### 8. Dashboard Shell
After authenticated login, show a real mobile dashboard shell with navigation targets for:
- My Resumes
- Optimize Resume
- Create Resume
- Templates
- ATS Check
- Cover Letters
- Interview Prep
- Applications

The first foundation may show empty states for domains not yet implemented, but do not show fake user metrics/data.

### 9. Navigation
Implement stable navigation for:
- auth flow
- dashboard
- feature placeholders/shell routes

Back navigation must not accidentally return authenticated users to stale login screens.

### 10. Theme
Create reusable Resumaire mobile theme aligned with approved UX direction:
- white/light-blue SaaS foundation
- blue/purple AI accents
- professional typography
- reusable spacing/radius tokens
- primary/secondary button styles
- input styles
- cards
- loading/error components

Do not hard-code random visual values repeatedly across screens.

### 11. Core Shared Widgets
Create only useful foundational widgets such as:
- primary button
- secondary button
- loading state
- error/retry state
- empty state
- standard app scaffold/header pattern where justified

### 12. Testing
Add tests for at least:
- auth/session state transitions
- login validation
- router redirect logic
- dashboard auth guard
- API error mapping where practical

## Out of Scope
Do not implement yet:
- full resume editor
- document parser
- DOCX processing
- complete ATS workflow
- complete interview engine
- production billing purchase flow
- app-store deployment

Those are future scoped tasks.

## Acceptance Criteria
1. Flutter project builds for Android in development environment.
2. iOS project structure is valid; build is verified on macOS/Xcode environment when available.
3. Application has a consistent state-management pattern.
4. API client uses gateway base URL from environment/configuration.
5. Login/register client integration follows approved API contract.
6. Access/refresh lifecycle is safely handled.
7. Tokens are not printed/logged.
8. App restores valid session on startup.
9. Invalid session redirects to login.
10. Dashboard requires authentication.
11. Dashboard exposes navigation to core Resumaire modules without fake data.
12. Theme/shared widgets are reusable.
13. Loading/error/retry states exist for auth/network operations.
14. `flutter format .` passes.
15. `flutter analyze` has no unresolved relevant errors.
16. Relevant `flutter test` tests pass.
17. No secrets are committed.
18. No direct production/store release occurs.

## Required Completion Report
Return:
- Flutter/Dart versions used
- state-management choice and rationale
- package dependencies introduced
- project structure
- screens created
- files changed
- auth/API integration status
- Android build result
- iOS validation result/status
- `flutter analyze` result
- `flutter test` result
- known limitations
- exact QA focus areas

Final development status is `DEV_COMPLETE` only after applicable acceptance criteria pass.
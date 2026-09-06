# Resumaire — Security Baseline

Status: AUTHENTICATION BASELINE LOCKED

## Core Principle
A resume contains private personal and employment information. Security and privacy are product requirements.

## Authentication
MVP uses a token-based authentication model.

### Access token
- Signed JWT
- Short-lived
- Default target TTL: 15 minutes
- Minimal claims only

Recommended claims:
- subject/user ID
- roles/authorities required for coarse access control
- issued-at/expiry
- token/session identifier where useful

Do not place resume content, job descriptions or other private user data in JWTs.

### Refresh token
- Opaque high-entropy token
- Default target TTL: 7 days
- Store only a secure hash of the refresh token server-side in Identity Service
- Rotate on successful refresh
- Support revocation/logout
- Track token family/session so stolen-token reuse can be detected/revoked where practical

### Passwords
- Identity Service is the only service that stores password hashes
- Use an established adaptive password hashing algorithm through Spring Security configuration
- Never log passwords

### Social login
Google/Apple/OIDC providers are a later feature. Identity Service architecture must remain extensible, but social login is not required for the first implementation slice.

## API Gateway
Spring Cloud Gateway validates bearer access tokens for protected routes and applies coarse route policy.

Gateway responsibilities do not replace domain authorization.

## Service-Level Authorization
Every business service validates the authenticated caller and enforces domain-specific access.

Every private resource operation must verify ownership or explicit permission server-side.

Required negative test:
1. User A creates a resume.
2. User B authenticates.
3. User B attempts GET/PATCH/DELETE/export/AI analysis using User A's resume ID.
4. Access must be denied without leaking private data.

Apply the same rule to:
- resume versions
- uploaded source documents
- generated exports
- ATS analyses
- cover letters
- interview-prep sessions
- application records

Never rely on hidden UI controls for authorization.

## Service-to-Service Calls
For MVP:
- internal services communicate over a private application network
- network location alone is not trust
- propagate the user access token only when downstream authorization genuinely requires end-user context
- use dedicated machine/service credentials for calls without an end-user principal
- downstream services validate caller identity/scope/authority

Do not share user passwords, refresh tokens or signing secrets between services.

A later platform may introduce workload identity or mTLS when infrastructure warrants it.

## Logout / Revocation
Logout revokes the active refresh-token session/family.

Short-lived JWT access tokens may remain cryptographically valid until expiry, so privileged/sensitive flows should be designed with this bounded window in mind. If a later requirement demands immediate access-token revocation, introduce a deliberate revocation/session check rather than ad-hoc distributed blacklists.

## Secrets
- Use environment/secret-manager configuration.
- Never commit AI provider keys, JWT signing keys, service credentials, DB credentials or store-signing material.
- Never log access tokens, refresh tokens or passwords.
- Rotate compromised secrets through an operational runbook.

## Input Validation
Validate API input at trust boundaries.

Define limits for:
- free-text fields
- job descriptions
- AI prompt inputs
- filenames/metadata
- pagination/filter values

Reject malformed/unsupported content predictably.

## File and PDF Security
Uploaded Office/PDF files are untrusted input.

Required controls:
- private resumes and generated PDFs must not use permanent public object URLs
- extension + MIME validation
- upload-size limits
- malware scanning hook
- reject macro-enabled formats in MVP unless explicitly supported
- isolate document conversion/rendering
- do not execute embedded scripts/macros
- random non-user-controlled storage keys
- short-lived signed access or authenticated delivery where appropriate
- deletion/retention must account for all derived objects

## AI Privacy and Safety
- Send only data necessary for the requested AI operation.
- Do not log full resume/job-description content by default.
- Validate structured model output.
- AI output does not bypass application validation or authorization.
- AI suggestions require explicit acceptance/save before replacing authored content.
- Unsupported work experience must never be silently fabricated.
- Define provider data-retention/privacy configuration before production launch.

## Abuse / Rate Control
Apply rate/usage controls to:
- login/password-reset endpoints
- token refresh abuse patterns
- AI endpoints
- large document operations
- export/conversion jobs

Return consistent 429 behavior where rate limits apply.

## Web
- TLS only in deployed environments.
- Narrow CORS configuration to approved origins.
- Do not use wildcard credentialed CORS.
- Protect access/refresh token handling according to the final browser storage/session implementation.
- If cookies are used for refresh/session state, configure Secure/HttpOnly/SameSite and applicable CSRF controls.

## Mobile
- Store sensitive token/session material using platform-appropriate secure storage.
- Do not persist secrets in plain preferences/files/logs.
- Use the same access/refresh/revocation semantics as web.

## Logging
Allowed examples:
- request ID
- resource IDs where policy permits
- endpoint
- duration
- response status
- dependency/provider latency

Avoid:
- passwords
- JWTs
- refresh tokens
- secrets
- full resume text
- full job descriptions
- unnecessary PII

## Release Security Gate
No release with known:
- cross-user resource access
- exposed secrets
- authentication bypass
- unrestricted private-file access
- refresh-token replay flaw without mitigation
- document macro/script execution path

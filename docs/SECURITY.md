# Security Baseline

## Core Principle
A resume contains private personal and employment information. Security and privacy are product requirements.

## Authentication
Architecture must select and document the concrete authentication mechanism. Regardless of implementation:
- credentials must use established password hashing/provider flows
- tokens/sessions must have bounded lifecycle
- logout/revocation behavior must be defined
- clients must not receive internal secrets

## Authorization and Object Ownership
Every private resource operation must verify ownership or explicit permission server-side.

Required negative test:
1. User A creates a resume.
2. User B authenticates.
3. User B attempts GET/PATCH/DELETE/export/AI analysis using User A's resume ID.
4. Access must be denied without leaking private data.

Never rely on hidden UI controls for authorization.

## Secrets
- Use environment/secret-manager configuration.
- Never commit AI provider keys, signing secrets, DB credentials or store signing material.
- Never log access/refresh tokens or passwords.

## Input Validation
Validate API input at trust boundaries. Define limits for long free-text fields and job descriptions. Reject malformed/unsupported content predictably.

## File and PDF Security
- Private resumes and generated PDFs must not use permanent public object URLs.
- Validate file type/size when uploads exist.
- Use short-lived signed access or authenticated delivery where appropriate.
- Deleting a user/resource must account for associated stored objects according to retention policy.

## AI Privacy and Safety
- Send only data necessary for the requested AI operation.
- Do not log full resume/job-description content by default.
- Validate structured model output.
- AI output does not bypass application validation or authorization.
- AI suggestions require explicit user acceptance/save before replacing authored content.
- Define provider data-retention/privacy configuration before production launch.

## Abuse/Rate Control
Apply rate/usage controls to expensive AI endpoints and authentication endpoints where justified. Return consistent 429 behavior.

## Web/Mobile
- Use TLS in deployed environments.
- Store mobile credentials/tokens using platform-appropriate secure storage.
- Define CORS narrowly for web deployment rather than wildcard credentialed access.
- Protect browser applications according to the selected session/token model, including CSRF controls when applicable.

## Logging
Allowed examples: request ID, user/resource IDs where policy permits, endpoint, duration, status, provider latency.
Avoid: passwords, tokens, secrets, full resume text, full job descriptions, unnecessary PII.

## Release Security Gate
No release with known cross-user access, exposed secrets, authentication bypass or unrestricted private-file access.

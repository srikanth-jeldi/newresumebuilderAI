# ADR-002 — Runtime Versions and Authentication Baseline

Status: ACCEPTED
Date: 2026-09-04

## Context
Resumaire needs a stable Java/Spring baseline for multiple backend services and a consistent authentication model for responsive web and Flutter Android/iOS clients.

The development environment already uses Java 21 LTS.

## Decision

### Runtime and framework
- Java 21 LTS
- Spring Boot 4.1.1
- Spring Cloud 2025.1.2 (Oakwood) for Spring Cloud components such as Gateway
- Maven dependency management with pinned versions

### Authentication
- short-lived signed JWT access token
- rotating opaque refresh token
- refresh tokens stored hashed in Identity Service
- default access-token TTL target: 15 minutes
- default refresh-token TTL target: 7 days
- logout/revocation applies to the refresh-token session/family
- social login is a later feature

### Authorization
- Gateway validates protected-route bearer tokens and coarse route access
- each business service independently enforces resource ownership and domain authorization
- service-to-service requests require authenticated caller context; private network placement alone is not treated as trust

## Rationale
Java 21 is already available and is an LTS runtime. Spring Boot 4.1.1 is the selected stable Spring Boot release, and Spring Cloud 2025.1.2 supports the Spring Boot 4.1 line. A short-lived JWT plus rotating server-tracked refresh token keeps normal API calls stateless while preserving logout/revocation control for long-lived sessions.

Service-level authorization is mandatory because gateway-only authorization cannot safely enforce ownership of resumes, versions, ATS reports, cover letters or application records.

## Consequences
Positive:
- one runtime baseline across services
- shared dependency compatibility baseline
- web/mobile share auth semantics
- scalable access-token validation
- explicit refresh/logout/revocation model
- strong protection against ID-based cross-user access when service rules are implemented correctly

Costs/risks:
- refresh-token rotation and reuse detection add Identity Service state
- JWT access tokens are normally valid until their short expiry after logout
- services must consistently implement authorization rather than assuming Gateway protection is enough

## Rejected Alternatives
- Java 17: supported but unnecessary because Java 21 LTS is already the project environment and target.
- Long-lived JWT without refresh state: weaker revocation/session control.
- Gateway-only authorization: cannot enforce domain ownership safely.
- Social login as first dependency: adds launch complexity without being necessary for the first vertical slice.

## Review Trigger
Review this ADR if:
- Spring release compatibility requires an upgrade
- immediate access-token revocation becomes a product requirement
- enterprise SSO/OIDC becomes a primary requirement
- deployment platform provides workload identity/mTLS that should replace service credentials

# ADR-011 — CI/CD, Environments and Deployment Strategy

Status: ACCEPTED

## Context
Resumaire is an early-stage startup with strict budget constraints but needs a professional delivery pipeline for multiple Spring Boot services, a Next.js web app, Flutter mobile apps, PostgreSQL, object storage and a document-conversion worker. The system must support safe automated validation, reproducible deployments, rollback, secret separation and production approval without introducing Kubernetes or expensive CI/CD platforms at MVP.

## Decision
Use **GitHub Actions + Docker + Docker Compose + one cost-efficient VPS for early production**. Maintain clear DEV, STAGING and PRODUCTION environments. Production deployment requires a human approval gate.

Do not use Kubernetes for MVP.

## Environment Model

### DEV
Purpose:
- developer local work
- agent development
- unit/integration testing

Components:
- Docker Compose
- PostgreSQL local container
- MinIO local object storage
- local or sandbox AI provider credentials
- local document-conversion worker

Rules:
- synthetic/test data only
- no production secrets
- safe to reset databases

### STAGING
Purpose:
- integration QA
- release candidate validation
- smoke/regression tests
- payment webhook testing using provider sandbox/test mode

Initial startup approach:
- may share the same VPS as production only if it is isolated by separate containers, networks, databases/schemas, buckets and secrets
- preferably separate small VPS once budget allows

Rules:
- no production customer data
- separate credentials from production
- deploy automatically after approved branch/merge policy if desired

### PRODUCTION
Purpose:
- real users

Rules:
- protected environment
- manual approval required before deployment
- production secrets only in protected GitHub Environment / server secret configuration
- backups and rollback readiness required

## Git Branch Strategy

Recommended startup model:

```text
main                     # protected, releasable
feature/...              # developer/agent work
fix/...                  # bug fixes
setup/...                # architecture/platform setup
```

Avoid a permanent `develop` branch unless the team actually benefits from it. For a small team, short-lived feature branches + PRs into `main` are simpler.

## Pull Request CI Pipeline

Every PR must run relevant checks before merge.

### Backend Services
For changed Spring Boot services:

1. checkout
2. setup Temurin Java 21
3. restore Maven dependency cache
4. `mvn -B -ntp verify`
5. run unit tests
6. run integration tests where configured
7. validate Flyway migrations against ephemeral/test PostgreSQL
8. dependency/vulnerability scan where practical
9. build service artifact/JAR
10. build Docker image as validation

Do not push production image from untrusted PR context.

### Web / Next.js
For changed web app:

1. setup Node LTS pinned by project
2. install dependencies using lockfile
3. lint
4. type-check
5. unit/component tests
6. production build
7. Playwright smoke tests where applicable

### Flutter
For changed mobile app:

1. setup pinned Flutter version
2. `flutter pub get`
3. `dart format --output=none --set-exit-if-changed .`
4. `flutter analyze`
5. `flutter test`
6. Android debug/build validation

Run iOS build validation on macOS runner when the workflow/release stage requires it. Do not pay for excessive macOS CI minutes for every trivial PR if budget is constrained; use targeted paths and release validation.

### Architecture / Documentation
For agent/architecture docs:
- markdown/link checks if configured
- no build required unless code/config changed

## Path-Based CI
Use changed-path detection so a change to the web app does not rebuild every backend service and Flutter app unnecessarily.

Example:

```text
services/identity-service/**     -> identity CI
services/resume-service/**       -> resume CI
services/ai-career-service/**    -> AI service CI
services/career-tracking/**      -> tracking CI
web/**                            -> web CI
mobile/**                         -> Flutter CI
document-worker/**                -> conversion worker CI
```

This reduces CI time and cost.

## Artifact and Image Strategy

### MVP Container Registry
Prefer GitHub Container Registry (GHCR) for application images while repository/GitHub plan terms remain suitable.

Image tags should include immutable commit SHA.

Example:

```text
ghcr.io/<owner>/resumaire-identity:<git-sha>
ghcr.io/<owner>/resumaire-resume:<git-sha>
ghcr.io/<owner>/resumaire-ai-career:<git-sha>
ghcr.io/<owner>/resumaire-web:<git-sha>
```

Never deploy using only `latest` as the source of truth.

Optional human-readable tags such as `staging` or release versions may point to the same immutable image.

## Main Branch Build Pipeline

After a PR is merged into `main`:

1. run required CI checks again or rely on required merge checks according to policy
2. build changed production Docker images
3. tag images with commit SHA
4. push images to GHCR
5. generate deployment metadata/release candidate
6. deploy automatically to STAGING
7. run staging smoke tests
8. run API health checks
9. run critical browser flow smoke tests
10. publish deployment result

If staging validation fails, do not promote to production.

## Production Deployment Flow

```text
PR merged to main
      ↓
CI green
      ↓
Build immutable images
      ↓
Push GHCR
      ↓
Deploy STAGING
      ↓
Smoke / QA checks
      ↓
Manual Production Approval
      ↓
Pull exact SHA-tagged images on VPS
      ↓
Run safe DB migrations
      ↓
Docker Compose rollout
      ↓
Health checks
      ↓
Production smoke tests
      ↓
SUCCESS or ROLLBACK
```

## Deployment Mechanism

For MVP use a deployment script on the production VPS invoked securely from GitHub Actions or by a controlled SSH/runner mechanism.

The deploy script should:

1. authenticate to GHCR with least privilege
2. pull exact image tags
3. validate required environment configuration
4. back up/confirm database backup status when a risky migration is present
5. run Flyway migration steps in controlled order
6. start/update Docker Compose services
7. wait for health/readiness endpoints
8. run smoke checks
9. fail the deployment if critical health checks fail
10. record deployed commit SHA

Do not copy source code manually to production as the normal deployment process.

## Docker Compose Production Rules

Use a dedicated production compose file or overlays.

Requirements:
- restart policy
- resource limits where practical
- health checks
- internal private network for services
- expose only reverse proxy/gateway publicly
- document worker isolated from public network
- persistent volumes only where needed
- logs rotated/bounded

## Reverse Proxy / TLS
Use a lightweight reverse proxy such as Caddy or Nginx in front of the public web/gateway endpoints.

Responsibilities:
- TLS termination
- HTTPS redirect
- domain routing
- request size limits aligned with upload policy
- security headers where appropriate

Use automated TLS certificates where supported.

## Database Migration Policy

Flyway migrations run per service/schema.

Rules:
- migrations must be forward-compatible where possible
- no destructive column/table removal in the same release that removes application dependency unless migration plan is explicitly reviewed
- large/data-changing migrations require staging test
- production migration failure stops deployment
- never use Hibernate auto-DDL to mutate production schema

Recommended production setting:

```text
hibernate.ddl-auto=validate
```

or equivalent strict non-mutating configuration.

## Rollback Strategy

Application rollback must be simple:

1. identify last known-good commit SHA
2. pull previous image tags
3. redeploy previous compose image references
4. run health/smoke checks

Database rollback is harder; therefore migration design should prioritize backward-compatible forward migrations.

Do not promise automatic DB rollback for every migration.

For destructive/data migrations:
- backup first
- explicit rollback/forward-fix plan
- human review required

## Secrets

Use GitHub Environments / encrypted repository secrets for CI/CD values where appropriate.

Production secrets should include only necessary deployment references. Prefer secrets resident on the server/secret store rather than passing every production secret through CI logs/processes.

Never store in Git:
- DB passwords
- JWT signing private keys/secrets
- Razorpay keys/webhook secrets
- AI provider API keys
- object-storage credentials
- SMTP credentials
- mobile signing credentials

## GitHub Environment Protection

Define:

```text
staging
production
```

Production environment:
- manual reviewer/approval where plan/features allow
- protected secrets
- only approved branches/workflows deploy

Even if GitHub plan limitations prevent a specific environment-protection feature, preserve the human approval step in the workflow/process.

## CI Security

Required practices:
- pin major action versions; review third-party actions
- minimum `permissions:` in GitHub Actions
- do not expose secrets to fork/untrusted PR jobs
- use OIDC/workload identity instead of long-lived cloud credentials when the chosen provider supports it
- do not echo secrets
- scan accidental secret commits
- use Dependabot/Renovate later if helpful and low-noise

## Test Layers

### PR Gate
Fast enough for normal development:
- compile/build
- unit tests
- selected integration tests
- lint/type/analyze

### Staging Gate
- API smoke
- authentication flow
- resume upload/parse smoke
- ATS job creation/status smoke
- document export smoke
- critical web E2E

### Release Gate
Before production:
- staging green
- QA status acceptable
- no BLOCKER/CRITICAL defects
- migrations reviewed
- backup/recovery risk understood
- human approval

## Mobile CI/CD

### Android
Initial pipeline:
- PR: analyze/test/debug-build
- release branch/tag: signed AAB build using protected signing credentials
- Google Play upload remains a deliberate release action, not automatic from every merge

### iOS
Initial pipeline:
- PR: analyze/test
- release: macOS runner/Xcode archive using protected signing credentials
- TestFlight/App Store publishing requires explicit release approval

Mobile store production releases must not be fully autonomous in the early stage.

## Release Versioning
Use semantic-style application releases when stable, e.g. `v0.1.0`, `v0.2.0`, `v1.0.0`.

Every release should map to:
- Git tag
- commit SHA
- container images
- migration set
- release notes

## Observability After Deployment
Immediately verify:
- gateway health
- service health/readiness
- DB connectivity
- object-storage connectivity
- AI provider connectivity (safe probe if used)
- error rate
- latency
- worker/job queue failures

Deployment success is not merely `docker compose up` returning zero.

## Cost Discipline
MVP should avoid:
- Kubernetes control-plane/cluster costs
- paid CI service when GitHub Actions quota is sufficient
- separate VPS per microservice
- separate staging infrastructure until usage/revenue justifies it
- rebuilding unchanged components

Optimize pipeline with:
- Maven/npm/Flutter caching
- path filters
- concurrency cancellation for superseded PR runs
- small immutable images
- scheduled heavy scans rather than every tiny change where appropriate

## Failure Handling

### CI failure
Block merge.

### Staging deploy failure
Do not permit production promotion.

### Production health failure
Stop rollout and redeploy previous known-good images when safe.

### Migration failure
Stop deployment immediately; investigate before continuing.

### Document worker failure after deploy
Application may remain available if safe, but export operations show degraded status and alerting should fire.

## Future Scale Path
When scale/reliability requirements justify it:
- separate staging VPS
- managed container platform
- blue/green or rolling deployment tooling
- Kubernetes only if operational value exceeds complexity
- managed secrets/observability
- queue/broker if async workload warrants it

Do not migrate simply because those tools are fashionable.

## Final Decision
**GitHub Actions for CI/CD, Docker/GHCR for immutable artifacts, Docker Compose on a cost-efficient VPS for MVP production, automatic staging validation, manual production approval, explicit migration/rollback/health gates, and targeted Android/iOS release pipelines.**

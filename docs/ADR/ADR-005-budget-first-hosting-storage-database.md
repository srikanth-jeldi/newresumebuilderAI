# ADR-005 — Budget-First Hosting, Database and Object Storage

Status: ACCEPTED

## Context
Resumaire is an early-stage startup product. Infrastructure must be inexpensive, easy to operate, and safe enough for private resume/document data. The architecture should avoid unnecessary managed-service cost before product usage justifies it.

## Decision
Use a staged cost strategy.

### Local development
- Docker Compose
- Local PostgreSQL container
- MinIO for S3-compatible object storage
- All Spring Boot services run locally or as containers
- LibreOffice document-conversion worker in an isolated container

This keeps local development effectively free apart from the developer machine and AI API usage.

### Early hosted MVP / private beta
Preferred low-cost deployment:
- One small VPS running the Spring Boot services, API Gateway, document worker and supporting containers through Docker Compose
- Managed/free-tier PostgreSQL where practical; Neon is the preferred startup option initially
- Cloudflare R2 for private resume/document object storage
- Web frontend deployed separately on a low-cost/free static/Next.js-compatible platform where practical, or on the same VPS if necessary

Do not deploy one paid VM/container per microservice during MVP. Logical microservice boundaries remain separate applications, but several services may share the same VPS host while traffic is low.

### PostgreSQL
Preferred startup option: Neon PostgreSQL.

Reasons:
- Free plan exists and is suitable for initial development/private beta usage
- Serverless/usage-oriented cost model reduces idle cost
- PostgreSQL is fully aligned with the selected backend stack

Data-boundary policy:
- Start with one PostgreSQL project/cluster where necessary for cost control
- Use separate schemas per service:
  - identity
  - resume
  - ai_career
  - career_tracking
- Separate Flyway history/migrations per service
- No direct cross-service table access

Move to separate databases/projects only when security, load, scaling, operational isolation or team ownership justifies the extra cost.

### Object storage
Preferred production object storage: Cloudflare R2 Standard.

Reasons:
- S3-compatible API
- private buckets
- low storage cost
- no Internet egress fee from R2
- meaningful free monthly allowance suitable for an early product

Bucket/object policy:
- private by default
- random non-guessable object keys
- short-lived signed access where required
- ownership metadata stored in Resume Service
- source resume is immutable
- generated versions and exports stored separately

Recommended prefixes:
- requirements-source/
- resume-source/
- resume-versions/
- exports/
- cover-letters/

### VPS hosting
For the first always-on production-like deployment, prefer a small Linux VPS rather than separate managed compute instances per service.

Initial sizing target:
- minimum practical baseline: approximately 4 GB RAM for a very small beta, but document conversion plus multiple JVMs may be tight
- preferred small-beta target: 8 GB RAM if all Spring services + PostgreSQL-side clients + document worker share one host

Where the user already has a suitable VPS provider/account, reuse it if pricing and resources are competitive rather than introducing another provider.

Example budget providers may include Hostinger VPS or Hetzner Cloud. Provider choice is operational, not coupled to application code.

### Render/free compute
Free hosted compute may be used for demos/dev previews, but not as the primary production architecture because free instances may sleep, have persistence limitations or other restrictions.

## Cost Guardrails
- No Kubernetes for MVP
- No managed Kafka/RabbitMQ for MVP
- No paid Redis unless a measured need appears
- No separate production VM per microservice initially
- No expensive observability SaaS by default; start with application metrics/logs and add managed tooling when required
- Apply AI request budgets, rate limits and model routing because AI usage may become the dominant variable cost
- Store binary files in object storage, not PostgreSQL
- Add lifecycle/retention rules for old generated exports when product policy permits

## Backup and Reliability
Free/cheap does not mean no backups.

Minimum production requirements:
- PostgreSQL backup/export strategy documented before public launch
- object storage retention/deletion policy
- source resume immutability
- infrastructure configuration stored in Git where safe
- secrets never committed

If a free database tier does not provide the recovery guarantees required for public production, upgrade the database before launch rather than accepting data-loss risk.

## Upgrade Triggers
Upgrade infrastructure when one or more of these occur:
- CPU/RAM saturation
- document jobs queue for unacceptable durations
- database storage/free-tier limits approach
- connection limits become restrictive
- R2 free usage is exceeded materially
- uptime/SLA requirements increase
- customer data volume makes free-tier recovery insufficient
- service isolation is needed for independent scaling

## Consequences
Benefits:
- very low starting infrastructure cost
- architecture remains production-oriented
- microservice boundaries are preserved without paying for many hosts
- easy path from local Docker Compose to a VPS
- storage/database can scale independently later

Trade-offs:
- one VPS is a shared failure domain
- free database tiers have limits
- more operations responsibility than fully managed compute
- later migration to separate service hosts may be required

These trade-offs are accepted for the startup/MVP phase.

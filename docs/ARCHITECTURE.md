# Resumaire — Architecture Baseline

Status: TECHNOLOGY STACK LOCKED — service/API details still refined through ARCH-001.

Brand: **Resumaire — An EpitomeHub® Product**

## Final Technology Direction

### Backend
- Java 21 LTS (Eclipse Temurin; development machine currently reports 21.0.6)
- Spring Boot
- Spring Security
- Spring Data JPA / Hibernate
- Spring Cloud Gateway
- Maven
- Jakarta Validation
- Spring Boot Actuator

Pin exact framework/library patch versions in dependency management and upgrade deliberately through reviewed PRs rather than floating versions.

### Database
- PostgreSQL
- Flyway for versioned schema migrations
- One PostgreSQL cluster is acceptable initially, but each service must own its schema/data boundary. No service may directly manipulate another service's tables.

### Web Frontend
- Next.js
- React
- TypeScript
- Tailwind CSS
- Responsive desktop/tablet/mobile web experience

### Mobile
- Flutter
- Android
- iOS
- Same versioned backend contracts as the web application

### Document Processing
- docx4j as the primary DOCX/OOXML fidelity engine, subject to a fidelity spike against representative resumes
- Apache POI only for operations where it is demonstrably sufficient
- Apache PDFBox for PDF text/metadata/layout-oriented parsing where appropriate
- LibreOffice headless, isolated in a controlled conversion worker/container, for DOCX-to-PDF rendering unless the fidelity spike selects a better deterministic renderer

### Storage
- Private S3-compatible object storage
- MinIO for local/dev
- Production may use Amazon S3 or another compatible private object store
- Source resumes, derived versions and exports are private by default

### AI
- Provider abstraction; business code must not depend directly on one model vendor
- Structured JSON/schema-constrained responses where supported
- Deterministic post-validation of AI outputs
- AI powers field-level suggestions, tailoring, ATS qualitative guidance, cover letters, interview questions and mock-interview evaluation
- AI never bypasses authorization or silently fabricates professional experience

### Infrastructure
- Docker for all backend services and document workers
- Docker Compose for local integration development
- GitHub + pull-request workflow
- CI/CD with build/test/security gates
- Kubernetes is not required for MVP; adopt only when deployment/scale/operations justify it

---

# Service Architecture

Use a small, explicit microservice architecture. Do not create a service for every screen or entity.

```text
                    Web — Next.js / React
                           |
                    Flutter Android/iOS
                           |
                           v
                 Spring Cloud API Gateway
                           |
          +----------------+------------------+
          |                |                  |
          v                v                  v
  Identity Service   Resume Service    AI Career Service
          |                |                  |
          |                |                  +-- ATS analysis
          |                |                  +-- field AI suggestions
          |                |                  +-- resume tailoring
          |                |                  +-- cover letters
          |                |                  +-- interview questions
          |                |                  +-- mock interview evaluation
          |                |
          |                +-- resume CRUD/versioning
          |                +-- templates
          |                +-- JD/requirements intake
          |                +-- document parsing
          |                +-- project selection
          |                +-- DOCX fidelity/editing
          |                +-- PDF import/export orchestration
          |
          +----------------------------------------------+
                           |
                           v
                 Career Tracking Service
                 +-- applications
                 +-- resume version used
                 +-- interview dates/status
                 +-- notes/next actions

Supporting components:
- PostgreSQL
- Private object storage
- Isolated document conversion worker
- External AI provider(s)
```

## Why these boundaries

### 1. API Gateway
Responsibilities:
- single public API entry point
- routing
- CORS policy
- common request/correlation IDs
- coarse rate limiting where appropriate
- authentication-token propagation/verification pattern agreed with Identity Service

The gateway must not become the business-logic layer.

### 2. Identity Service
Responsibilities:
- registration
- login/logout
- token/session lifecycle
- password reset
- social login later if enabled
- account/profile security
- roles/permissions

No other service stores passwords.

### 3. Resume Service
This is the main deterministic domain service and intentionally owns the document workflow to avoid premature fragmentation.

Responsibilities:
- user resume metadata
- original resume upload references
- immutable source document
- resume versions
- template catalog/selection
- pasted/uploaded job description records
- parsed resume structure
- detected experience/projects
- user-selected project scope
- accepted/rejected AI change proposals
- DOCX fidelity edits
- PDF import orchestration
- export jobs/status
- user document library

The service calls AI Career Service for AI inference but remains authoritative for what is persisted into a user's resume.

### 4. AI Career Service
Responsibilities:
- JD/profile matching
- ATS-oriented analysis
- contextual field-level suggestions
- bullet rewrite/improve/shorten/expand/quantify suggestions
- job-specific tailoring proposals
- skill-gap analysis
- cover-letter generation
- dynamic interview-question generation from JD + resume + projects + seniority + technologies
- scenario/production/system-design question generation
- mock-interview rubric evaluation and follow-up generation

It returns proposals/results. It does not directly mutate Resume Service data.

### 5. Career Tracking Service
Responsibilities:
- saved jobs/applications
- company/role/job URL
- application status
- dates/interview schedule metadata
- resume version used
- cover-letter version used
- notes
- next actions

Keep this separate because its lifecycle is distinct from resume document processing.

---

# Primary Product Journey

```text
Landing
  ↓
Register / Login
  ↓
Dashboard
  ├── My Resumes
  ├── Templates
  ├── ATS Scores
  ├── Cover Letters
  ├── Interview Prep
  └── Applications

Optimize Existing Resume:
Paste/upload job description
  ↓
Upload existing resume
  ↓
Parse document + detect sections/projects
  ↓
AI job/profile analysis + ATS-oriented score
  ↓
Select latest 1 / latest 2 / latest N / manual projects
  ↓
Generate structured change proposals
  ↓
Build Resume OR Customize Resume
  ↓
Field-level AI editor
  ↓
Review/Accept/Reject
  ↓
Final ATS analysis
  ↓
Preview
  ↓
Save version
  ↓
Download DOCX / PDF
  ↓
Cover Letter / Interview Prep / Application Tracking
```

---

# Resume Persistence and Versioning

Every authenticated user owns a private resume library.

Store metadata for:
- original uploaded resume
- created-from-template resume
- draft versions
- tailored versions
- final/exported versions
- target job/JD associated with each tailored version
- ATS analysis history
- timestamps and version lineage

The original upload is immutable.

Every Build Resume/Apply Accepted Changes operation creates or updates a derived version. The system must make it possible to identify what changed from the source/previous version.

Required user actions:
- open
- edit
- rename
- duplicate
- tailor for another job
- run ATS analysis
- create cover letter
- open interview prep
- download DOCX/PDF
- archive/delete according to retention policy

---

# Resume Templates

Provide a broad first-party template catalog with Resumaire designs. Do not clone proprietary third-party templates pixel-for-pixel.

Initial categories:
- ATS Friendly
- Modern
- Professional
- Executive
- Simple
- One-column
- Two-column
- Entry-level
- Experienced
- Creative where ATS/readability trade-offs are clearly communicated

Templates must be represented as versioned rendering definitions rather than hard-coded page screenshots.

---

# Requirements Intake

Accept:
- pasted job description / roles & responsibilities
- uploaded TXT
- uploaded DOCX
- uploaded PDF
- optional job URL later when supported safely/reliably

Normalize these into a target-job model consumed by ATS and AI Career Service.

---

# Resume Parsing

Identify, with confidence metadata where possible:
- contact/header
- professional summary
- skills
- experience/project blocks
- company/client/project names
- titles
- dates
- responsibility bullets
- achievements
- education
- certifications
- custom sections

Low-confidence project boundaries require user confirmation before project-targeted tailoring.

---

# AI Change Proposal Contract

AI returns structured proposals, not uncontrolled source-document writes.

Each proposal identifies:
- target resume version
- section/project
- source paragraph/bullet anchor
- original text
- proposed text
- reason
- matched JD requirement/keyword
- factual-support classification
- confidence
- whether explicit confirmation is required

Proposal classifications:
- supported rewrite
- supported keyword alignment
- measurable-impact prompt
- requires confirmation
- unsupported / do not add

Resume Service persists only user-approved/build-approved changes.

---

# Field-Level AI

Every meaningful editable resume field may expose contextual AI actions where useful:
- Suggest
- Improve
- Rewrite
- Shorten
- Expand
- Strengthen action verbs
- Improve clarity
- Quantify impact
- Match JD
- ATS keyword alignment
- Fix grammar

AI must return a visible proposal. It never silently overwrites user text.

---

# DOCX Fidelity Engine

DOCX is the preferred high-fidelity editable source.

Primary approach:
- preserve the original OOXML package
- edit targeted paragraphs/runs/structures where possible
- avoid plain-text flatten-and-rebuild for high-fidelity mode

Evaluate docx4j against a representative fidelity test suite containing:
- tables
- bullets/numbering
- custom styles
- headers/footers
- multi-column layouts
- page breaks
- fonts
- tab stops
- text boxes/complex constructs where feasible

If a construct cannot be preserved safely, surface the limitation rather than silently corrupting layout.

---

# PDF Import and Export

PDF input is primarily a read/import path. Arbitrary PDF files are not guaranteed to be editable with exact layout preservation.

For PDF import:
- validate file
- extract text/layout metadata
- detect sections/projects
- build canonical resume structure
- use best-effort editable reconstruction

Recommend original DOCX when near-exact source-format preservation is required.

For PDF output:
- render from the final approved DOCX/canonical document
- isolate LibreOffice headless or chosen renderer in a controlled worker/container
- do not run embedded macros/scripts

---

# ATS Architecture

Maintain two concepts separately:

1. **Job-Specific ATS Match Score**
   - requires a target job/JD
   - keyword/skill coverage
   - role alignment
   - section/readability checks
   - supported experience mapping

2. **Generic Resume Quality Score**
   - optional product feature
   - no target job required
   - completeness/readability/action-bullet/style checks

Do not market either as guaranteed compatibility with every proprietary ATS.

Use deterministic scoring components where measurable and AI for qualitative guidance. Keep scoring logic explainable and versioned.

---

# Interview Preparation Architecture

Question generation inputs:
- target JD
- resume technologies
- selected projects
- candidate seniority/years of experience
- role expectations
- gaps from analysis

Question categories:
- important technical
- scenario-based
- project/resume-based
- technology deep dive
- production troubleshooting
- system design
- architecture/trade-offs
- coding where relevant
- behavioral

Senior candidates must be weighted toward scenario, production, system-design and trade-off questions rather than definition-only questions.

Store generated interview-prep sessions/results per user when the user chooses to save them.

---

# PostgreSQL Ownership

Each microservice owns its data boundary.

Initial practical deployment may use one PostgreSQL server/cluster with separate schemas/databases, but direct cross-service table access is prohibited.

Use Flyway migrations per service.

Suggested ownership:
- identity schema/database → Identity Service
- resume schema/database → Resume Service
- ai-career schema/database → only persisted AI jobs/usage/prep records if needed
- career-tracking schema/database → Career Tracking Service

Binary documents must not be stored as ordinary PostgreSQL BLOBs unless a later measured requirement justifies it.

---

# Object Storage

Private object categories:
- `requirements-source/`
- `resume-source/`
- `resume-versions/`
- `exports/`
- `cover-letters/`

Use authenticated backend access or short-lived signed URLs.

Never use permanent public URLs for private resumes.

---

# Security

Required:
- server-side ownership checks on every user-owned resource
- secure password hashing in Identity Service
- short-lived access tokens/session policy with explicit refresh/revocation design
- input validation
- MIME + extension + size validation for uploads
- malware scanning hook
- reject macro-enabled documents in MVP unless explicitly supported
- isolated document conversion
- no embedded macro/script execution
- private object-storage keys
- no passwords/tokens/private resume payloads in logs
- rate/usage controls on costly AI operations

User A must never access User B's resume, version, export, ATS analysis, cover letter, interview-prep session or application record by changing an ID.

---

# Observability

All services:
- structured logs
- correlation/request ID propagation
- Actuator health/readiness endpoints
- latency/error metrics
- dependency latency metrics

Document flow:
- document-processing job ID
- parse duration
- fidelity/render failures
- conversion duration

AI flow:
- provider/model identifier
- latency
- failure category
- token/usage/cost telemetry without logging private resume content

---

# Local Development

Use Docker Compose to run integration dependencies/services.

Expected local components:
- api-gateway
- identity-service
- resume-service
- ai-career-service
- career-tracking-service
- PostgreSQL
- MinIO
- document conversion worker
- web app

Flutter app may run from the developer machine/device/emulator against the local gateway.

---

# Build and Repository Rules

Backend services:
- Java 21
- Maven
- independent Spring Boot applications
- dependency versions managed centrally where practical
- one service may not import another service's internal persistence/domain implementation

Web:
- Next.js + React + TypeScript
- shared design tokens/components
- API calls through versioned contracts

Mobile:
- Flutter
- shared contract semantics
- mobile-specific UX rather than compressed desktop screens

---

# Decisions Still Open for ARCH-001

The following remain deliberate architecture tasks, not developer guesses:
1. Exact Spring Boot/Spring Cloud compatible versions to pin
2. Auth implementation details: access/refresh/session/revocation and social login phase
3. Inter-service authentication/authorization model
4. Whether service communication is REST-only for MVP or any async job channel is justified after latency measurements
5. AI provider(s), model policy and abstraction interface
6. Exact PostgreSQL database-vs-schema isolation strategy
7. Production object-storage provider
8. Hosting/deployment target
9. Upload-size limits and data-retention policy
10. Document-processing sync vs async thresholds
11. Exact DOCX fidelity library spike result
12. Exact PDF rendering/conversion worker hardening
13. Subscription/payment/usage-metering phase

Do not allow Dev Agents to independently invent these decisions when they materially affect multiple services.

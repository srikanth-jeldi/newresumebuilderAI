# ADR-001 — Resumaire Technology Stack

Status: ACCEPTED

## Context
Resumaire requires a production-oriented web application, Android/iOS application, Spring-based backend, persistent user resume/version storage, high-fidelity document processing, AI-assisted resume tailoring, ATS analysis, interview preparation and application tracking.

The development environment already has Eclipse Temurin OpenJDK 21 LTS available.

## Decision

### Backend
Use Java 21 LTS with Spring Boot and Maven.

Use:
- Spring Security
- Spring Data JPA / Hibernate
- Spring Cloud Gateway
- Flyway
- Spring Boot Actuator

### Microservices
Use a deliberately small service set:
1. API Gateway
2. Identity Service
3. Resume Service
4. AI Career Service
5. Career Tracking Service

Do not split document parsing, templates, ATS scoring, interview questions or every UI domain into separate services unless measured operational or scaling needs justify it.

### Database
Use PostgreSQL. Each service owns its data boundary and Flyway migrations. A single PostgreSQL cluster may host separate schemas/databases initially, but cross-service direct table access is forbidden.

### Web
Use Next.js + React + TypeScript + Tailwind CSS.

### Mobile
Use Flutter for Android and iOS, consuming the same backend API semantics as web.

### Documents
- docx4j: primary DOCX/OOXML fidelity candidate
- Apache POI: limited/supporting use where adequate
- Apache PDFBox: PDF parsing support
- isolated LibreOffice headless worker: initial DOCX-to-PDF rendering candidate

A document fidelity spike must validate representative real resumes before declaring full support.

### Storage
Use private S3-compatible object storage. Use MinIO for local development. Production provider remains deployment-specific.

### AI
Use a provider abstraction with structured outputs and validation. AI returns proposals/results and does not directly mutate user-owned resume data.

### Infrastructure
Use Docker and Docker Compose for development/integration. Do not require Kubernetes for MVP.

## Rationale
- Java 21 is already available and is an LTS runtime suitable for long-lived backend development.
- Spring Boot matches the desired engineering stack and provides mature security, persistence, validation and observability support.
- PostgreSQL is a strong relational fit for user-owned transactional metadata and versioning.
- Next.js/React provides a strong SEO-capable responsive web stack for a public SaaS product and authenticated app.
- Flutter allows one mobile codebase for Android/iOS while preserving mobile-native UX.
- A small microservice set keeps requested microservice architecture without creating unnecessary distributed-system overhead.
- Document processing remains isolated because uploaded Office/PDF content and office rendering carry distinct reliability/security risks.

## Consequences
### Positive
- Clear technology ownership for Dev Agents.
- Shared web/mobile API contracts.
- Java/Spring ecosystem can support document, auth, persistence and AI orchestration requirements.
- Services can be scaled independently later where justified.

### Negative / Costs
- Microservices introduce network, deployment and observability complexity compared with a modular monolith.
- DOCX fidelity requires specialized testing; no library guarantees perfect handling of all Word constructs.
- Flutter and Next.js require separate UI implementations/design systems to remain aligned.
- Multiple services require disciplined API contracts and database ownership.

## Guardrails
- No service-per-entity design.
- No Kubernetes merely to satisfy an architecture label.
- No direct database-table access across services.
- No direct AI writes into final user documents.
- No public object-storage URLs for private resumes.
- No claim of exact arbitrary-PDF edit fidelity.

## Follow-up Architecture Tasks
- Pin mutually compatible Spring Boot/Spring Cloud versions.
- Define authentication/token lifecycle.
- Define inter-service security.
- Perform DOCX fidelity spike.
- Define AI provider abstraction.
- Define production deployment/storage target.

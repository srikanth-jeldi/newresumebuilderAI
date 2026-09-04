# New Resume Builder AI — Architecture Baseline

Status: DRAFT — to be refined by ARCH-001.

## Architecture Direction
Start with a modular monolith backend rather than microservices. The project is new and there is no demonstrated scale or team boundary that justifies distributed-service operational complexity.

```text
Responsive Web Client
       |
       | HTTPS
       v
API / Modular Backend Application
       |
       +-- Identity & Access
       +-- Requirements Intake
       +-- Document Upload
       +-- Resume Parsing
       +-- Resume Structure Detection
       +-- Project Selection
       +-- AI Tailoring
       +-- Change Review / Versioning
       +-- DOCX Editing
       +-- PDF Rendering
       +-- ATS Analysis
       |
       +------ Relational Database
       |
       +------ Private Object Storage
       |
       +------ AI Provider Adapter
```

## Primary User Journey
```text
Paste/upload role responsibilities
             |
             v
       Upload resume
             |
             v
 Parse document + detect sections/projects
             |
             v
 Ask user: latest 1 / latest 2 / latest N / choose projects
             |
             v
 Match target responsibilities to existing experience
             |
             v
 Generate proposed changes
             |
       +-----+------+
       |            |
       v            v
 Build Resume   Customize Resume
       |            |
       +-----+------+
             v
        Preview changes
             |
             v
      Export DOCX / PDF
```

## Recommended Logical Modules
### Identity
Users, authentication, session/token lifecycle and access policy.

### Requirements Intake
Accept pasted text and uploaded requirement documents. Normalize all sources into a single target-requirements model for downstream matching.

Supported initial requirement formats:
- TXT
- DOCX
- PDF

### Document Upload
Owns private upload lifecycle, content-type/size checks, object-storage references, malware scanning hook, upload status and source-file immutability.

### Resume Parsing
Parses uploaded resume content into a structured internal representation while preserving a reference to the source document.

The parser should identify, with confidence metadata where possible:
- personal/contact area
- summary
- skills
- experience/project blocks
- company/client/project names
- job titles
- dates
- responsibility bullets
- education/certifications

### Project Selection
Stores the user's explicit tailoring scope:
- latest project
- latest 2 projects
- latest N projects
- selected project IDs

Low-confidence project detection must require user confirmation before document mutation.

### AI Tailoring
Produces structured change proposals, not uncontrolled free-text mutation.

Each proposal should identify:
- source project
- source paragraph/bullet anchor
- original text
- proposed text
- matched target responsibility/skill
- confidence
- whether user confirmation is required

The AI module must not invent unsupported experience. Missing target responsibilities should be suggestions requiring explicit confirmation rather than automatic claims.

### Resume Versioning
The original upload is immutable. Every accepted/build operation produces a derived version with an audit trail of changes.

### DOCX Fidelity Engine
DOCX is the preferred editable source.

High-fidelity editing should preserve the original OOXML document package and modify only targeted paragraphs/runs/structures when possible rather than reconstructing the whole document from a generic template.

Architectural preference when using a Java backend:
- evaluate docx4j for OOXML-level manipulation
- use Apache POI only where it provides sufficient fidelity for a specific operation
- avoid flattening the document into plain text before rewriting

The engine must preserve as much as practical:
- section/page settings
- styles
- fonts
- numbering/bullets
- tables
- paragraph spacing
- headers/footers
- ordering

Complex Word constructs (text boxes, SmartArt, embedded drawings, unusual fields) require explicit fidelity testing.

### PDF Import
PDF should be treated primarily as a read/import source, not as a guaranteed editable source format.

For PDF:
- extract text/layout information using a PDF parser
- detect sections/projects
- build an internal representation
- produce a best-effort reconstructed document for editing/export

Do not claim exact in-place PDF formatting preservation for arbitrary files.

If fidelity is critical, ask for the original DOCX.

### Export
DOCX export:
- generated from the versioned edited document package where the source is DOCX

PDF export:
- render the final DOCX or canonical document representation to PDF through a controlled server-side renderer

Evaluate LibreOffice headless or another deterministic office renderer for DOCX->PDF conversion in the deployment environment. Conversion workers should be isolated because office-document rendering expands the attack surface.

### ATS Analysis
Use deterministic extraction/matching for measurable keyword/skill coverage where practical and AI for qualitative rewriting/suggestions. Do not present an invented universal ATS score as scientifically precise.

## Data Store
Use one relational database for transactional product metadata. The binary source/generated documents live in private object storage rather than database BLOBs unless a later requirement justifies otherwise.

## Object Storage
Private storage only for resumes and generated files.

Recommended object categories:
- `requirements-source/`
- `resume-source/`
- `resume-versions/`
- `exports/`

Access via authenticated backend or short-lived signed URLs.

## Security Boundary for Document Processing
Uploaded Office/PDF files are untrusted input.

Required controls:
- extension + MIME validation
- file-size limits
- malware scanning hook
- reject macro-enabled formats in MVP unless explicitly supported
- sandbox/isolate document conversion/rendering
- do not execute embedded scripts/macros
- random non-user-controlled storage keys
- per-user authorization on all document operations

## API
Version paths under `/api/v1`. Web and later mobile clients share semantics.

## Authentication
Exact mechanism remains an ARCH-001 decision. All source files, parsed structures, versions and exports require server-side ownership checks.

## AI Reliability
- provider timeout
- bounded retry only where safe
- structured/schema-validated output
- token/cost budgets
- PII-aware logging
- provider/model configured outside code
- no direct AI writes to source files
- deterministic post-validation before accepting proposal payloads

## Observability
- structured logs
- request/correlation IDs
- document-processing job IDs
- parser/render duration metrics
- failure reason categories
- AI latency/error/cost indicators
- conversion queue depth if async processing is introduced

## Deployment Baseline
Initial components:
- responsive web app
- modular backend application
- relational database
- private object storage
- isolated document-render/conversion capability
- external AI provider

Do not introduce Kubernetes merely for MVP. Containerization is sufficient initially.

## Open Decisions for ARCH-001
1. Backend language/framework and versions
2. Relational database selection
3. Authentication/session model
4. Web frontend stack
5. DOCX library selection after fidelity spike (docx4j vs alternatives)
6. PDF text/layout extraction library
7. DOCX-to-PDF rendering approach
8. Object-storage provider
9. AI provider abstraction and structured-output model
10. Hosting/deployment target
11. Maximum upload sizes and retention policy
12. Whether document processing is synchronous for MVP or job-based after size/latency measurement

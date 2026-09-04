# New Resume Builder AI — Product Definition

## Product Goal
Build a document-first AI resume tailoring platform that lets a user paste or upload target job roles/responsibilities, upload an existing resume, choose which projects should be tailored, preview changes, and download the result with minimal extra work.

The primary experience is not "create a resume from scratch". It is "take my existing resume, understand its structure, intelligently tailor selected project responsibilities, preserve my format as much as technically possible, and let me export Word/PDF".

## Primary Users
- Experienced professionals tailoring an existing resume for a specific job description.
- Job seekers who receive role/responsibility requirements and want to align their resume quickly.
- Users maintaining multiple resume variants for different roles.
- Users who need ATS-oriented guidance without manually reformatting their document.

## Core Landing-Page Flow
The first page should use a clean premium resume-builder layout inspired by modern resume-builder products, but with original branding, copy, assets and design tokens.

### Step 1 — Target Role / Responsibilities Input
The landing page contains a large text area:
- Label: `Paste role & responsibilities / job description`
- User can paste plain text.

Beside/below it provide:
- `Browse document` / drag-and-drop upload.
- Initial supported requirement files: `.docx`, `.pdf`, `.txt`.

A user may either paste requirements, upload a requirements document, or use both.

### Step 2 — Existing Resume Upload
After requirements input is accepted, show the resume upload area prominently:
- `Upload your resume`
- Prefer `.docx` for high-fidelity editing.
- Accept `.pdf` for parsing/import with explicit fidelity limitations.

### Step 3 — Resume Understanding
After resume upload, the system should:
1. Parse the document.
2. Detect major sections.
3. Detect work-experience/project blocks where possible.
4. Extract project/company/title/date/responsibility text.
5. Compare current experience with target responsibilities.
6. Never silently overwrite the source file.

### Step 4 — Ask Where to Apply Changes
Before generating final changes, ask the user where the target responsibilities should be incorporated.

Required choices:
- Latest project only
- Latest 2 projects
- Latest N projects
- Select projects manually

If project detection confidence is low, show extracted project blocks and ask the user to confirm them before editing.

### Step 5 — Primary Actions
Only after both requirements and resume are available, enable:

#### Build Resume
Automatically creates a tailored draft for the selected projects while preserving truthful experience.

The system should:
- Map target responsibilities/skills to existing resume experience.
- Rewrite/strengthen matching bullets.
- Insert relevant missing wording only when it can be supported by existing experience or explicit user confirmation.
- Avoid duplicate bullets across projects.
- Preserve dates, companies, titles and factual project metadata unless the user explicitly edits them.
- Show proposed changes before final save/export.

#### Customize Resume
Open an interactive editor where the user can:
- Review AI suggestions one by one.
- Accept/reject/edit individual bullets.
- Move a suggestion to a different selected project.
- Edit sections manually.
- Preview the resulting document.

### Step 6 — Export
Allow download as:
- Word `.docx`
- PDF `.pdf`

The user should not need to copy content into another editor.

## Document-Fidelity Policy
### DOCX
DOCX is the preferred editable source and high-fidelity path.

Goal:
- retain page setup
- fonts
- paragraph styles
- headings
- tables
- spacing
- headers/footers where technically supported
- existing section order
- original visual structure as far as the document permits

The system should edit the source document structure rather than rebuilding it from a generic template when high-fidelity mode is possible.

### PDF
PDF is accepted for reading/import, but arbitrary PDFs cannot be guaranteed to support exact in-place editing while preserving the original layout.

For PDF uploads:
- extract text and layout information when possible
- detect sections/projects
- offer a best-effort reconstructed editable resume
- clearly identify when exact-format preservation is not technically possible
- recommend uploading the original DOCX when the user needs near-exact format preservation

Do not falsely promise pixel-perfect editable PDF preservation.

## Experience-Integrity Rule
The product must not automatically fabricate professional experience.

Target job-description responsibilities are requirements, not proof that the user performed those responsibilities.

The system may:
- improve wording of existing work
- map equivalent skills/activities
- reorganize relevant truthful bullets
- suggest missing responsibilities for user confirmation

The system must not silently claim a technology, project responsibility, achievement or metric that is unsupported by the uploaded resume or explicit user confirmation.

## MVP Outcomes
A user should be able to:
1. Paste or upload target role/responsibility requirements.
2. Upload an existing resume.
3. Parse and preview detected resume sections/projects.
4. Select latest one/two/N or manually selected projects for tailoring.
5. Generate a tailored draft.
6. Review before accepting changes.
7. Customize individual suggestions.
8. Export DOCX and PDF.
9. Preserve original DOCX formatting as much as technically feasible.
10. Receive ATS-oriented matching feedback.

## Secondary Resume-Creation Capabilities
The architecture may later support:
- create a resume from scratch
- resume templates
- reusable profile data
- multiple resume variants
- cover-letter generation

These are secondary to the document-first tailoring workflow for the initial product.

## AI Features
MVP:
- responsibility-to-experience matching
- bullet rewriting
- duplicate detection
- skill/keyword matching
- project-targeted suggestions
- ATS-oriented analysis
- structured change proposals

AI output is always a draft until accepted by the user or applied through an explicit Build Resume confirmation step.

## Platforms
Initial priority:
- Responsive web application

Later using the same backend contracts:
- Android via Flutter
- iOS via Flutter

## Product Principles
- Existing resume format preservation is a first-class requirement.
- User owns and controls resume content.
- Original uploaded file remains immutable.
- Generated revisions are versioned derivatives.
- Private resume data must remain private.
- AI must not silently fabricate experience.
- Web/mobile share backend contracts.
- Export quality is a core feature.

## Out of Scope for Initial Architecture
- Recruiter marketplace
- Enterprise ATS product
- Social-network features
- Real-time multi-user collaboration
- Premature microservice/Kubernetes complexity

## Initial Success Criteria
- End-to-end requirements upload/paste -> resume upload -> project selection -> tailored preview -> DOCX/PDF export works.
- Source resume remains unchanged.
- DOCX output retains the original document's important visual structure.
- User can see exactly what AI changed.
- Cross-user document access is blocked.
- AI/provider errors never destroy uploaded or edited user work.

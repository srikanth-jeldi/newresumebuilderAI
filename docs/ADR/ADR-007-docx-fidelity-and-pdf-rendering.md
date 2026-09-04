# ADR-007 — DOCX Fidelity and PDF Rendering

Status: ACCEPTED

## Context
Resumaire's core differentiator is the ability to read an existing resume, tailor selected experience/project content, preserve the source format as closely as technically possible, and return a usable Word/PDF document without forcing the user to reformat it manually.

Arbitrary Office and PDF documents are complex. A "flatten to text and rebuild" approach would destroy formatting fidelity. A safer design is required.

## Decision

### 1. DOCX is the canonical high-fidelity editable path
For `.docx` input, preserve the original OOXML package and mutate only targeted document structures when possible.

Primary library: **docx4j**.

Apache POI may be used selectively for operations where it is proven to preserve the required formatting, but it is not the primary fidelity engine.

Do not convert DOCX to plain text and reconstruct the whole resume when the source is available.

### 2. Original source is immutable
The uploaded source DOCX is never overwritten.

Every generated result is a derived `ResumeVersion` with lineage back to the immutable source.

### 3. Change anchoring
Parser/editor must retain stable anchors where possible for editable content:
- section identifier
- paragraph index / OOXML relationship
- table/cell location when applicable
- bullet/list metadata
- original text checksum or equivalent guard

AI suggestions operate against canonical semantic fields, but the Resume Service applies accepted changes back to anchored OOXML structures.

If the expected source anchor no longer matches because the document changed, fail safely and require re-parse instead of writing to the wrong paragraph.

### 4. Fidelity preservation targets
The DOCX engine should preserve, as far as supported:
- page size and margins
- section breaks
- paragraph styles
- run/font formatting
- numbering and bullets
- indentation and tab stops
- line/paragraph spacing
- tables and cell formatting
- headers and footers
- hyperlinks
- page breaks
- column configuration
- ordering of document elements

Complex constructs such as text boxes, SmartArt, floating drawings, embedded objects, unusual fields or custom XML may not always survive targeted modification. These cases must be detected/tested and surfaced as a fidelity limitation rather than silently corrupted.

### 5. Fidelity test suite is mandatory before release
Create representative DOCX fixtures covering at minimum:
- simple one-column resume
- two-column resume
- table-based layout
- custom bullets/numbering
- custom fonts/styles
- headers/footers
- explicit page breaks
- tab-stop layouts
- multi-page experienced resume
- complex layout with text boxes/drawings where feasible

For each fixture validate:
1. source opens successfully in Word/LibreOffice
2. parser detects target project/responsibility
3. targeted text is replaced correctly
4. unrelated content remains unchanged
5. output DOCX opens without repair warning
6. page layout remains materially equivalent
7. PDF render is visually usable

Do not ship high-fidelity claims until this suite passes an agreed threshold.

### 6. PDF input policy
PDF input is accepted for parsing/import, not guaranteed exact in-place editing.

Use **Apache PDFBox** initially for text extraction and layout-oriented metadata where practical.

For PDF uploads:
- parse text and positions where possible
- detect sections/projects
- build a canonical editable resume representation
- preserve the uploaded PDF as immutable source
- reconstruct a derived editable resume using a Resumaire template or best-effort source-inspired layout

If the user wants the closest match to the original document, explicitly recommend uploading the source DOCX.

Never market arbitrary PDF editing as pixel-perfect preservation.

### 7. PDF output pipeline
Preferred pipeline for derived DOCX resumes:

`final approved DOCX -> isolated LibreOffice headless worker -> PDF`

LibreOffice conversion must run outside the primary API JVM in a worker/container because Office-file rendering is heavier and expands the attack surface.

The worker must:
- use a temporary per-job working directory
- have no access to production secrets beyond what it needs
- receive only the input object/job reference
- have CPU/memory/time limits
- disable macro/script execution
- delete temporary files after completion
- upload the generated PDF to private object storage
- report success/failure to the durable job record

### 8. Rendering failures
If conversion fails:
- source/derived DOCX remains intact
- job becomes `FAILED`
- store a machine-readable failure category
- allow bounded retry for transient failures
- do not endlessly retry malformed documents
- offer DOCX download even if PDF generation failed, when DOCX itself is valid

### 9. Security
Treat all uploaded Office/PDF files as untrusted.

MVP rules:
- reject macro-enabled Office formats such as `.docm`
- validate extension and MIME type
- enforce size limits from ADR-006
- include malware-scanning integration point
- never execute embedded macros/scripts
- randomize storage keys
- never expose permanent public object URLs
- use short-lived signed URLs or authenticated streaming

### 10. Canonical structured representation
Resume Service should maintain both:
- source/derived document object references
- a canonical structured resume model used by web/mobile editors and AI services

The canonical model does not replace the original OOXML fidelity path. It exists so UI and AI can operate on stable semantic fields such as summary, skills, projects, bullets and education.

### 11. Preview strategy
For editing UX:
- use a fast web preview derived from canonical resume data/templates where possible
- final export validation must use the actual generated DOCX/PDF, not only the browser preview

For source-fidelity mode, final pre-download preview should represent the rendered derived document closely enough to catch pagination/overflow issues.

### 12. Cost strategy
All selected tools are open-source/self-hostable for MVP:
- docx4j
- Apache PDFBox
- LibreOffice headless

No paid document-conversion API is required initially.

A managed conversion provider may be evaluated later only if measured fidelity/reliability/operational cost justifies it.

## Consequences

### Benefits
- strong preservation path for DOCX resumes
- no dependency on expensive document API in MVP
- isolated risky/heavy conversion process
- safe immutable source/version model
- truthful limitation for PDF input

### Costs
- OOXML manipulation requires careful engineering
- fidelity testing is non-trivial
- LibreOffice worker consumes memory/CPU
- some complex Word constructs will require special handling or documented limitations

## Release Gate
The document engine is not production-ready until:
- representative DOCX fidelity fixtures pass
- generated DOCX files open without corruption/repair warnings
- user-approved content only is applied
- unrelated formatting/content remains unchanged within the defined fidelity threshold
- worker isolation and cleanup are verified
- PDF render failures do not cause data loss

## Decision Summary
**DOCX: docx4j targeted OOXML editing, immutable source, derived versions.**

**PDF input: PDFBox parse/import, best-effort reconstruction; no exact-edit promise.**

**PDF output: isolated LibreOffice headless conversion worker.**

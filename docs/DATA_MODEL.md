# Data Model Baseline

Status: DRAFT — finalize keys/types/constraints during ARCH-001.

## User
- id
- email (unique)
- password/auth-provider representation according to auth architecture
- display name
- status
- created_at
- updated_at

## Resume
- id
- user_id / owner_id
- title
- template_id
- status
- created_at
- updated_at
- optional optimistic-lock/version field

Index/query expectations include owner + updated time for dashboard listing.

## Resume Profile / Contact Data
Keep sensitive personal fields explicitly owned by a resume/user and avoid duplicating unnecessary PII.

Candidate fields:
- full_name
- professional_title
- email
- phone
- location
- website/portfolio
- professional links

## Resume Section Entries
The architect must choose between typed tables per section (preferred when fields/query behavior differ materially) or a controlled structured representation. Do not choose generic JSON merely for implementation speed without considering validation and evolution.

Core types:
- professional summary
- work experience
- education
- skills
- projects
- certifications

All entries require stable IDs and ordering semantics where reordering is supported.

## Template
- id
- name
- status
- supported capabilities/version

Template source/layout artifacts may live in application assets/code rather than the DB when they are version-controlled product assets.

## Export
Persist export metadata only if needed for download history or async processing.
Candidate:
- id
- resume_id
- user_id
- type
- status
- object_key
- created_at
- expires_at where applicable

## AI Suggestion / Analysis
Do not automatically persist every raw prompt/response. Architecture must define privacy, product value, retention and cost/debug requirements first.

If ATS analysis history is a product feature, candidate fields:
- id
- resume_id
- owner_id
- score
- structured result
- model/prompt version metadata as appropriate
- created_at

## Ownership Rule
Every private resume-related entity must be reachable to an authenticated owner through server-side authorization checks. IDs are not authorization.

## Migration Rules
- Version all schema changes.
- Prefer backward-compatible migrations.
- Never rely on manual production schema edits.
- Add indexes based on real query paths.
- Define deletion/retention policy before storing long-lived generated/AI artifacts.

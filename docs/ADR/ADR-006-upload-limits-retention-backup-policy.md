# ADR-006 — Upload Limits, Retention and Backup Policy

Status: ACCEPTED FOR MVP

## Context
Resumaire stores private resumes, job descriptions, generated resume versions, cover letters and exports. The startup must control storage cost while avoiding accidental user-data loss and avoiding indefinite storage of unnecessary temporary artifacts.

## Decision Summary
Use conservative upload limits, private object storage, versioned user-owned documents, short-lived temporary artifacts, explicit deletion states, and low-cost automated backups for transactional metadata.

## Upload Limits
### Resume uploads
- Accepted MVP formats: DOCX and PDF.
- Maximum file size: 10 MB per resume upload.
- DOCX is preferred for high-fidelity editing.
- Macro-enabled Office formats are rejected in MVP.
- Password-protected/encrypted documents are rejected unless a later feature explicitly supports them.

### Job description uploads
- Accepted MVP formats: TXT, DOCX and PDF.
- Maximum file size: 5 MB per job-description upload.
- Pasted job-description text should have a configurable maximum character limit; initial target: 50,000 characters.

### Cover-letter / export artifacts
Generated documents follow server-controlled output limits rather than arbitrary user uploads.

## Per-User MVP Storage Guardrail
Initial soft quota target: 250 MB of stored user-owned document data per free account.

This is a product guardrail, not a permanent commercial limit. It exists to protect startup storage cost and abuse risk.

When approaching the quota, the product should show storage usage and allow users to remove old versions/exports before hard failure.

Do not delete user-owned source resumes automatically merely because the soft quota is approached.

## Storage Classes / Artifact Categories
### Durable user-owned artifacts
Keep until user deletes the relevant resource/account or an explicit product retention rule applies:
- original uploaded resume
- user-saved resume versions
- final resume exports intentionally saved to My Resumes / My Documents
- saved cover letters
- saved application attachments/links that are part of the user workspace

### Re-creatable artifacts
May be expired automatically to reduce storage cost:
- transient PDF preview render
- temporary conversion intermediates
- uncommitted generated export candidates
- parser scratch files
- AI working files that are not a user-saved document

Default MVP retention target for temporary/re-creatable artifacts: 24 hours unless a workflow requires less.

### Draft history
Keep user-visible resume versions that the product exposes as version history.

Do not retain every internal autosave forever. Internal autosave checkpoints may be compacted so long as user-visible history and crash recovery remain reliable.

## Source Immutability
Original uploaded resumes are immutable.

Every accepted tailoring/build operation creates a derived version or an explicit new revision. Never overwrite the original object in place.

## Delete Behaviour
Deleting a resume from the UI should first create a soft-delete state so accidental deletion can be recovered.

Initial MVP recycle-bin target: 30 days.

After the recovery window, permanently delete:
- database metadata according to dependency rules
- source/derived files owned only by that deleted resource
- exports that no longer have another valid reference

Account deletion must trigger deletion of user-owned private data after any legally or operationally required short processing window. The exact public privacy-policy wording must match the implemented behaviour before launch.

## Temporary File Handling
Document workers must use isolated temporary directories.

Temporary local files must be deleted after job completion/failure and also cleaned by a periodic stale-file cleanup process.

Never depend on worker-local disk as the durable source of truth.

## Object Storage
Use private S3-compatible storage.

Local/dev:
- MinIO

Production startup default:
- low-cost S3-compatible private object storage selected by ADR-005

Object keys must be random/non-user-controlled and must not expose email addresses, resume titles or other private data in public URLs.

Access through authenticated backend delivery or short-lived signed URLs.

## Database Backup Policy
PostgreSQL contains authoritative metadata, ownership records, version lineage, job state, ATS results and application-tracking data. It requires automated backups.

Minimum MVP policy:
- provider-managed point-in-time recovery if the selected managed PostgreSQL plan supports it; otherwise
- automated daily logical backup/export
- retain at least 7 daily restore points for production once real users are onboarded
- keep backup credentials separate from application runtime credentials

Before paid launch or meaningful production traffic, perform an actual restore test. A backup that has never been restored is not considered validated.

## Object Storage Durability
Do not build a second full storage replica on day one solely for theoretical redundancy if it materially increases startup cost.

Instead:
1. Use a reputable object-storage provider with built-in durability.
2. Enable versioning/lifecycle features only when cost and provider capability justify them.
3. Keep database metadata sufficient to identify missing objects.
4. Add cross-provider/cross-region replication when usage/revenue justifies the additional cost or when business requirements demand it.

## Recovery Objectives
MVP targets are pragmatic, not enterprise promises.

Initial internal goals after production launch:
- RPO target for database metadata: 24 hours maximum if only daily backups exist; lower when provider PITR is available.
- RTO target: best effort within several hours for a startup-scale incident.

Do not publish stronger SLA promises until monitoring, backup and restore drills prove them.

## Privacy / AI Data Minimization
Do not retain raw AI request/response payloads indefinitely merely for debugging.

Store only what is needed for:
- user-visible saved result
- audit/change proposal
- usage/cost telemetry
- failure diagnosis without unnecessary private content

Full resume and job-description text must not be copied into ordinary logs.

## Cleanup Jobs
Implement scheduled cleanup for:
- expired temporary objects
- stale conversion artifacts
- abandoned upload sessions
- completed async-job scratch data
- expired soft-deleted resources after the recycle-bin window

Cleanup must be idempotent and ownership-safe.

## Monitoring
Track:
- total object-storage usage
- storage by artifact category
- failed deletions
- stale temporary objects
- backup success/failure
- last successful restore test date
- users approaching storage quota

## Cost Principle
Do not pay for large storage/backup systems before usage requires them, but do not sacrifice recoverability of production metadata to save a trivial amount.

Priority order:
1. user privacy
2. recoverability
3. correctness
4. cost optimization

## Consequences
### Benefits
- predictable storage cost
- safer upload attack surface
- recoverable accidental deletes
- original resume preserved
- temporary files do not accumulate indefinitely
- backup expectations are explicit

### Trade-offs
- 10 MB resume limit may reject unusually large graphical resumes
- 30-day recycle bin consumes some additional storage
- free/low-cost database plans may provide weaker backup guarantees than paid tiers
- object replication is deferred initially

## Review Trigger
Revisit this ADR when any of the following becomes true:
- free-account storage routinely approaches quota
- large enterprise resumes/documents require larger uploads
- production revenue supports stronger backup tiers
- compliance/customer contracts require stricter retention or deletion windows
- storage exceeds the initial provider free/low-cost tier

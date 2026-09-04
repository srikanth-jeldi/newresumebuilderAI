# API Contracts

Status: DRAFT CONTRACT MAP. Detailed schemas will be finalized by architecture tasks before implementation.

Base prefix: `/api/v1`

## Error Envelope
```json
{
  "code": "RESUME_NOT_FOUND",
  "message": "Resume was not found.",
  "requestId": "...",
  "fieldErrors": []
}
```

## Identity
- `POST /auth/register`
- `POST /auth/login`
- `POST /auth/refresh` if selected
- `POST /auth/logout`
- `GET /me`

## Tailoring Sessions
A tailoring session owns one target-requirements input, one source resume, detected project structure, selected tailoring scope, proposed changes and resulting versions.

- `POST /tailoring-sessions` — create a session
- `GET /tailoring-sessions/{sessionId}` — get owned session state
- `DELETE /tailoring-sessions/{sessionId}` — cancel/delete according to retention policy

Candidate create response:
```json
{
  "sessionId": "...",
  "status": "AWAITING_REQUIREMENTS"
}
```

## Target Requirements
### Paste text
- `PUT /tailoring-sessions/{sessionId}/requirements/text`

```json
{
  "text": "Target role and responsibilities..."
}
```

### Upload document
- `POST /tailoring-sessions/{sessionId}/requirements/uploads`

Supported MVP inputs: TXT, DOCX, PDF.

The server should normalize pasted/uploaded sources into structured target requirements.

## Source Resume Upload
- `POST /tailoring-sessions/{sessionId}/resume/uploads`

Supported MVP:
- DOCX — preferred high-fidelity editable source
- PDF — readable/import source with best-effort reconstruction

Candidate response:
```json
{
  "documentId": "...",
  "fileName": "resume.docx",
  "sourceType": "DOCX",
  "status": "PROCESSING"
}
```

## Parse/Structure Status
- `GET /tailoring-sessions/{sessionId}/structure`

Candidate response:
```json
{
  "status": "READY",
  "sourceFidelity": "HIGH",
  "projects": [
    {
      "projectId": "p1",
      "order": 1,
      "title": "Project Alpha",
      "company": "Example Corp",
      "role": "Senior Engineer",
      "dateRange": "2024 - Present",
      "confidence": 0.96
    }
  ]
}
```

## Project Selection
- `PUT /tailoring-sessions/{sessionId}/project-selection`

Examples:
```json
{
  "mode": "LATEST_N",
  "count": 2
}
```

or

```json
{
  "mode": "SELECTED",
  "projectIds": ["p1", "p3"]
}
```

Allowed modes:
- `LATEST_ONE`
- `LATEST_TWO`
- `LATEST_N`
- `SELECTED`

## Generate Tailoring Proposals
- `POST /tailoring-sessions/{sessionId}/proposals`

The response contains structured changes and never mutates the immutable source document directly.

Candidate change item:
```json
{
  "proposalId": "...",
  "projectId": "p1",
  "anchorId": "paragraph-or-bullet-anchor",
  "originalText": "Designed REST APIs...",
  "proposedText": "Designed and optimized secure REST APIs...",
  "matchedRequirement": "Design secure REST APIs",
  "confidence": 0.91,
  "requiresUserConfirmation": false
}
```

Unsupported/new experience must return `requiresUserConfirmation: true` and must not be silently inserted.

## Build Resume
- `POST /tailoring-sessions/{sessionId}/build`

Purpose: apply eligible/accepted proposals to a derived resume version.

Candidate request:
```json
{
  "proposalMode": "APPLY_SAFE_AND_CONFIRMED",
  "confirmedProposalIds": []
}
```

Candidate response:
```json
{
  "resumeVersionId": "...",
  "status": "READY_FOR_PREVIEW"
}
```

## Customize Resume
- `GET /tailoring-sessions/{sessionId}/proposals`
- `PATCH /tailoring-sessions/{sessionId}/proposals/{proposalId}` — edit/accept/reject/move proposal
- `POST /tailoring-sessions/{sessionId}/versions` — create/update a reviewed version

## Preview
- `GET /resume-versions/{versionId}/preview`

Preview implementation may return rendered pages/images or a secure document/PDF preview depending on the selected rendering architecture.

## Export
- `POST /resume-versions/{versionId}/exports`

```json
{
  "format": "DOCX"
}
```

or

```json
{
  "format": "PDF"
}
```

- `GET /resume-versions/{versionId}/exports/{exportId}`

Private downloads must use authenticated delivery or short-lived signed URLs.

## ATS Analysis
- `POST /resume-versions/{versionId}/ats-analyses`

Return measurable keyword/skill matching separately from qualitative AI recommendations. Do not imply a universal ATS score is guaranteed to match every employer ATS.

## Standard Status Guidance
- 200/201 success
- 202 accepted for asynchronous parse/render jobs
- 400 validation
- 401 unauthenticated
- 403 authenticated but unauthorized
- 404 resource not found
- 409 state/conflict
- 413 file too large
- 415 unsupported media type
- 422 file parsed but structure cannot be confidently understood
- 429 rate/usage limit
- 500 unexpected internal failure
- 503 provider/dependency unavailable

## Ownership Rule
Every session, uploaded source document, parsed structure, proposal, version, preview and export is private to the owning user unless a future explicit sharing feature is designed.

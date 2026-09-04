# API Contracts

Status: DRAFT CONTRACT MAP. Detailed schemas will be finalized by architecture tasks before implementation.

Base prefix: `/api/v1`

## Error Envelope
All clients should receive a consistent structured error rather than raw exceptions.

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
- `POST /auth/refresh` if token-based refresh is selected
- `POST /auth/logout`
- `GET /me`

## Resumes
- `POST /resumes` — create owned resume
- `GET /resumes` — list current user's resumes; paginated when needed
- `GET /resumes/{resumeId}` — get owned resume
- `PATCH /resumes/{resumeId}` — update resume metadata/content according to agreed mutation model
- `DELETE /resumes/{resumeId}` — delete/archive owned resume according to product policy
- `POST /resumes/{resumeId}/duplicate`

Every `{resumeId}` operation requires server-side ownership validation.

## Sections
Exact endpoint shape is pending architecture decision. Prefer either aggregate resume updates or explicit section resources consistently; do not mix patterns without reason.

Required capabilities:
- add section entry
- update section entry
- remove section entry
- reorder entries/sections

## Templates
- `GET /templates`
- `GET /templates/{templateId}` where detailed metadata is required
- `PATCH /resumes/{resumeId}/template` or equivalent agreed contract

## Export
Candidate contract:
- `POST /resumes/{resumeId}/exports`
- `GET /resumes/{resumeId}/exports/{exportId}` if asynchronous export is needed

Do not introduce async export until rendering latency/requirements justify it.

## AI Writing Assistance
Candidate contract:
`POST /resumes/{resumeId}/ai/suggestions`

The request must identify the target content and intent. Response returns suggestions/drafts; it must not silently persist over user content.

## ATS Analysis
Candidate:
`POST /resumes/{resumeId}/ats-analyses`

Example request:
```json
{
  "jobDescription": "..."
}
```

Candidate response:
```json
{
  "analysisId": "...",
  "score": 78,
  "matchedSkills": [],
  "missingSkills": [],
  "suggestions": []
}
```

Architecture must define which parts of score/matching are deterministic versus AI-generated.

## Standard Status Guidance
- 200/201 success
- 400 validation
- 401 unauthenticated
- 403 authenticated but unauthorized
- 404 resource not found
- 409 state/conflict
- 429 rate/usage limit
- 500 unexpected internal failure
- 503 provider/dependency unavailable

Detailed request/response schemas remain a required output of feature architecture before Dev handoff.

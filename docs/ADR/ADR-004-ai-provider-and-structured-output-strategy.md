# ADR-004 — AI Provider and Structured Output Strategy

Status: ACCEPTED

## Context
Resumaire uses AI for resume tailoring, field-level suggestions, ATS qualitative guidance, cover letters, interview questions, mock-interview evaluation and career guidance. These outputs affect user documents and therefore require predictable schemas, factual-safety checks and provider portability.

## Decision
Use an AI provider abstraction inside AI Career Service.

Primary provider for the first implementation:
- OpenAI API using schema-constrained structured outputs where supported.

Secondary/fallback provider capability:
- Google Gemini using JSON-schema structured outputs where supported.

Business services must never depend directly on provider-specific request/response objects.

## Provider Abstraction
Define a provider-neutral interface similar to:

```text
AiProvider
  analyzeJobAndResume(...)
  generateResumeSuggestions(...)
  generateAtsAnalysis(...)
  generateCoverLetter(...)
  generateInterviewQuestions(...)
  evaluateMockInterview(...)
```

Provider adapters:
- OpenAiProviderAdapter
- GeminiProviderAdapter

A future provider can be added without changing Resume Service contracts.

## Structured Output Rule
For production AI operations, prefer schema-constrained JSON output rather than free-form prose.

Example proposal schema:

```json
{
  "proposalId": "uuid",
  "targetSection": "EXPERIENCE",
  "projectId": "uuid",
  "sourceAnchor": "paragraph-17",
  "originalText": "...",
  "proposedText": "...",
  "reason": "...",
  "matchedRequirement": "...",
  "supportClass": "SUPPORTED_REWRITE",
  "confidence": 0.91,
  "requiresConfirmation": false
}
```

Allowed support classifications:
- SUPPORTED_REWRITE
- SUPPORTED_KEYWORD_ALIGNMENT
- MEASURABLE_IMPACT_PROMPT
- REQUIRES_CONFIRMATION
- UNSUPPORTED_DO_NOT_ADD

## Validation Pipeline
AI output is untrusted even when schema-valid.

Required pipeline:

```text
Provider response
    ↓
Schema validation
    ↓
Semantic validation
    ↓
Ownership/context validation
    ↓
Experience-integrity validation
    ↓
Policy/business-rule validation
    ↓
Persist/display proposal
```

Schema compliance alone is not sufficient.

## Experience Integrity
The model must never silently invent:
- employers
- projects
- technologies used
- achievements
- metrics
- responsibilities

A JD requirement that is not supported by the resume must be classified as either:
- REQUIRES_CONFIRMATION
- UNSUPPORTED_DO_NOT_ADD

Only explicit user confirmation can promote a missing responsibility into editable user-approved content.

## Model Selection Policy
Do not hard-code model names throughout business code.

Use configuration by capability, for example:

```text
AI_MODEL_RESUME_ANALYSIS=
AI_MODEL_RESUME_REWRITE=
AI_MODEL_ATS=
AI_MODEL_INTERVIEW=
AI_MODEL_MOCK_INTERVIEW=
```

Model changes require configuration/review, not source-code rewrites.

## Cost and Latency Controls
For every AI request capture:
- operation type
- provider
- model identifier
- latency
- token/usage metadata where available
- success/failure category
- retry count

Do not log full private resume or JD text.

Use bounded retries only for transient failures.

Use per-user rate/usage limits for expensive operations.

## Privacy
Send only the content required for the requested operation.

Do not send unrelated resumes, applications or historical data.

Provider retention/privacy settings must be reviewed before production launch.

## Prompt Management
Prompts must be versioned.

Recommended structure:

```text
ai/prompts/
  resume-analysis/
  resume-rewrite/
  ats-analysis/
  cover-letter/
  interview-questions/
  mock-interview/
```

Each prompt version should be testable against representative fixtures.

## Evaluation
Create regression fixtures for:
- 2–4 year candidate
- 5–8 year candidate
- 9–12+ year candidate
- Java/Spring backend role
- frontend role
- DevOps/cloud role
- resume with missing JD skills
- resume with conflicting/ambiguous project boundaries

Evaluate:
- factual faithfulness
- schema validity
- relevance
- duplicate suggestion rate
- seniority fit
- scenario-question quality
- hallucinated experience rate

Any hallucinated professional claim is a release-blocking defect for resume-generation flows.

## Fallback Behaviour
If the primary provider fails:
- retry only within bounded transient policy
- if secondary provider is enabled and contract-compatible, use it
- otherwise return a recoverable job failure
- never save partial or malformed AI output into the final resume

## Consequences
Benefits:
- provider portability
- predictable API contracts
- safer persistence
- better testability
- cost/quality routing later

Trade-offs:
- more adapter and validation code
- provider capabilities do not map perfectly
- fallback quality may differ

These trade-offs are acceptable because AI directly influences user-owned professional documents.

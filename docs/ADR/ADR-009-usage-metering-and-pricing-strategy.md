# ADR-009 — Usage Metering and Pricing Strategy

Status: ACCEPTED FOR MVP

## Context
Resumaire uses AI-heavy features (ATS analysis, resume tailoring, field-level suggestions, cover letters, interview preparation, mock interviews) and document-processing workloads. For an early-stage startup, unlimited free usage can create unpredictable cost before product-market fit is proven.

The commercial price itself should not be hard-coded before real usage/cost data is measured.

## Decision
Build a configurable entitlement and usage-metering system first. Launch with a conservative free tier and a paid tier structure whose exact INR/USD price remains configuration, not code.

### Initial Plans
1. FREE
2. PRO
3. TEAM / BUSINESS — later, not MVP

## FREE Plan — Initial Limits
These are launch defaults and must remain configurable:
- Up to 2 active resumes
- Up to 3 job-specific ATS analyses per month
- Up to 20 field-level AI suggestion actions per month
- Up to 2 full resume-tailoring runs per month
- Up to 1 AI cover letter per month
- Up to 25 generated interview questions per month
- Up to 1 mock interview session per month
- Up to 5 DOCX/PDF exports per month
- 250 MB soft storage quota
- Access to a limited subset of templates

The FREE plan must still demonstrate the end-to-end product value. Do not make it so restricted that users cannot experience the core workflow.

## PRO Plan — Entitlement Direction
PRO should provide substantially higher limits, not necessarily "unlimited" at launch.

Recommended configurable defaults:
- More active resumes and versions
- Higher ATS-analysis allowance
- Higher AI-suggestion allowance
- Higher full-tailoring allowance
- More cover letters
- More interview-question generations
- More mock interviews
- Higher export allowance
- Access to all standard templates
- Larger storage quota
- Priority processing only if infrastructure later supports it

Exact numeric PRO limits may be adjusted after beta telemetry.

## Why Not Hard-Code a Price Now
The final monthly/yearly price depends on:
- real model-token cost per feature
- document conversion CPU/RAM cost
- storage/egress cost
- support burden
- conversion rate
- regional payment fees/taxes

Therefore:
- plan prices must live in product/configuration data
- backend entitlement logic must use plan/feature identifiers, not price values
- final pricing should be approved only after 2–4 weeks of measured beta usage or a sufficient sample size

## Metered Feature Keys
At minimum meter these separately:
- ATS_ANALYSIS
- FIELD_AI_SUGGESTION
- FULL_RESUME_TAILORING
- COVER_LETTER_GENERATION
- INTERVIEW_QUESTION_GENERATION
- MOCK_INTERVIEW_SESSION
- DOCUMENT_EXPORT
- STORAGE_BYTES

## Usage Record
Track, where applicable:
- userId
- featureKey
- quantity
- timestamp
- job/request ID
- provider/model identifier for AI operations
- approximate tokens/usage/cost telemetry

Do not store full private resume/JD content in usage logs.

## Entitlement Check Flow
```text
Client action
   ↓
API request
   ↓
Authenticate user
   ↓
Load plan + entitlement
   ↓
Check remaining allowance
   ↓
Allowed? ── no ─→ 402/429-style product limit response + upgrade UI
   ↓ yes
Create job / perform action
   ↓
Record successful billable/metered usage
```

Do not consume quota for requests that fail before meaningful work starts. For AI/document jobs, define when usage is considered consumed and keep it idempotent to avoid double charging/counting on retries.

## Service Ownership
For MVP, keep entitlement/usage ownership inside Identity Service or a small dedicated module there unless billing complexity justifies a separate Billing Service later.

Do NOT create a separate microservice for billing on day one.

## Payment Provider
Payment-provider selection is a separate architecture decision. Keep billing integration behind an interface so the product can support regional providers later.

Potential future providers may include Razorpay for India and/or Stripe for broader markets, but no provider is locked by this ADR.

## UX Rules
- Show remaining usage clearly before a costly action where useful.
- Do not surprise the user after long processing with a hidden paywall.
- Explain what resets monthly.
- If a limit is reached, preserve all user data and drafts.
- Upgrade prompts must not block access to already-created documents.

## Cost Protection
- Per-user rate limits remain separate from monthly plan quotas.
- AI provider usage caps/circuit breakers should exist at system level.
- Admin must be able to disable or reduce expensive features without a code release if provider cost spikes.

## Beta Pricing Process
Before final paid price is locked:
1. Run beta with configurable free/early-access limits.
2. Measure average and 95th percentile AI cost per workflow.
3. Measure document-processing resource cost.
4. Estimate payment fees and taxes.
5. Set gross-margin target.
6. Propose monthly/annual price.
7. Revisit after real conversion and usage data.

## Consequences
### Positive
- protects startup budget
- avoids premature pricing guesswork
- makes limits configurable
- allows future plan experimentation
- avoids unlimited-AI cost blowups

### Negative
- requires entitlement and usage tracking from early development
- product needs clear quota UX

## Final Rule
Do not hard-code commercial price or "unlimited" AI promises into business logic. Build configurable plans, quotas and metering first, then price from real cost data.

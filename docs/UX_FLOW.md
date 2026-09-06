# Resumaire — UX Flow & Screen Contract

Status: MVP DESIGN CONTRACT

Brand: **Resumaire — An EpitomeHub® Product**

## Design Direction
Use leading resume-builder products only as references for hierarchy, whitespace, strong CTA placement, resume preview patterns and user-flow clarity.

Do not copy third-party branding, logos, proprietary illustrations, reviews, wording, assets or exact layouts.

Resumaire must have its own identity: premium white/light-blue SaaS foundation with blue/purple AI accents, strong typography, generous whitespace, rounded professional cards, explicit AI states and clear progress.

---

# Complete Screen Flow

```text
1. Home / Landing
   ↓
2. Paste Job Description / Upload JD
   ↓
3. Upload Existing Resume
   ↓
4. AI Analysis Results
   ↓
5. Select Projects / Experience to Tailor
   ↓
6. Build Resume OR Customize Resume
   ↓
7. Resume Editor with Field-Level AI
   ↓
8. Review AI Changes
   ↓
9. ATS Analysis
   ↓
10. Resume Preview
   ↓
11. Download DOCX / PDF
   ↓
12. Cover Letter
   ↓
13. Interview Preparation Dashboard
   ↓
14. Dynamic Question Practice
   ↓
15. Mock Interview
   ↓
16. Application Tracker
```

---

# Screen 1 — Home / Landing

## Purpose
Explain the product quickly and move the user into either creating/tailoring a resume or optimizing an existing one.

## Brand Header
- Resumaire logo
- `An EpitomeHub® Product`
- Resume
- Cover Letter
- Career Tools
- Interview Prep
- Resources
- Pricing if commercial model is enabled
- Login
- Get Started

## Hero
Suggested positioning:
`Build a Resume That Gets You Hired`

Supporting message should explain:
- AI-assisted resume tailoring
- ATS optimization
- real-experience-only generation
- interview preparation

Primary CTAs:
- `Create New Resume`
- `Optimize My Resume`

## Validation
No fake customer counts, company logos, ratings, testimonials or Trustpilot data may be shown unless verified and authorized.

## Next
Optimize My Resume → Screen 2.

---

# Screen 2 — Paste Job Description

## Purpose
Capture the exact target job/role.

## Inputs
Primary large textarea:
`Paste the full job description, role, or responsibilities here...`

Secondary options:
- Upload JD document
- Optional job-post link

Supported initial formats:
- TXT
- DOCX
- PDF

## States
- empty
- text entered
- document uploading
- parsing
- parsed
- unsupported file
- parse failed

## Validation
Continue remains disabled until usable job requirements are present.

## Next
Continue → Screen 3.

---

# Screen 3 — Upload Existing Resume

## Purpose
Read the user's real experience and preserve their source document for tailoring.

## Controls
- Drag/drop resume
- Browse Resume
- DOCX recommended
- PDF accepted

Microcopy:
`For the closest formatting match, upload the original Word (.docx) file.`

## States
- empty
- uploading
- parsing
- parsed
- low-confidence structure detection
- unsupported file
- password-protected/encrypted file
- parse failure

## Non-Negotiable
The uploaded source file is immutable. Derived versions must be created separately.

## Next
Successful parse → Screen 4.

---

# Screen 4 — AI Analysis Results

## Purpose
Compare target job with the candidate's real profile before altering the resume.

## Display
- Overall match score
- Skills match
- Experience alignment
- Education/certification alignment where relevant
- Role/seniority alignment
- Strong areas
- Missing or weakly represented requirements
- ATS keyword gaps
- Suggestions

## Gap Classification
Every requested responsibility/technology should be classified as one of:
- already supported by resume
- likely supported / needs user confirmation
- not supported / skill gap

Never convert an unsupported job requirement into claimed work experience automatically.

## Next
Continue → Screen 5.

---

# Screen 5 — Select Projects / Experience to Tailor

## Purpose
Give the user explicit control over where job-aligned changes may be proposed.

## Options
- Latest project
- Latest 2 projects
- Latest N projects
- Select specific projects manually
- All projects

For manual selection show:
- company/project
- role
- dates
- detected summary
- checkbox/toggle

If parser confidence is low:
`We found these experience/project sections. Confirm them before we update your resume.`

User must be able to correct detected project boundaries/titles.

## Validation
At least one editable target must be selected before tailoring starts.

## Next
Continue → Screen 6.

---

# Screen 6 — Build Resume / Customize Resume

## Build Resume
AI prepares a complete tailored draft using only supported or explicitly confirmed experience.

Processing stages should be visible:
1. Reading job description
2. Reading resume
3. Matching responsibilities and technologies
4. Mapping requirements to selected projects
5. Generating proposals
6. Validating factual support
7. Preparing derived document

## Customize Resume
Moves directly into guided suggestion-by-suggestion editing.

## Proposal Classes
- supported rewrite
- supported keyword alignment
- measurable-impact prompt
- requires confirmation
- unsupported / do not add

## Next
Build or Customize → Screen 7.

---

# Screen 7 — Resume Editor with Field-Level AI

## Purpose
This is a core Resumaire differentiator.

Every meaningful editable field should offer contextual AI assistance.

## AI Actions
Where appropriate show:
- AI Suggest
- Improve
- Rewrite
- Shorten
- Expand
- Strengthen action verbs
- Improve clarity
- Quantify impact
- Match JD
- ATS keyword alignment
- Fix grammar

## Covered Areas
- Professional summary
- Job title/headline
- Work experience bullets
- Project descriptions
- Skills
- Achievements
- Education descriptions where relevant
- Certifications descriptions where relevant
- Custom sections

## UX Pattern
Desktop:
- left navigation/section outline
- central editor
- live resume preview alongside or easily switchable

Every AI action produces a visible proposal. It must never silently overwrite text.

## Next
Review changes → Screen 8.

---

# Screen 8 — Review AI Changes

## Purpose
Make AI modifications transparent and reversible.

## Per Change
Show:
- Original
- Suggested version
- Why suggested
- Job requirement/keyword connection when relevant

Actions:
- Accept
- Reject
- Edit
- Regenerate
- Move suggestion to another selected project where appropriate

## Global Controls
- Apply accepted changes
- Undo
- Reset derived version to uploaded source

## Validation
Only accepted/edited changes are allowed into the final document.

## Next
Apply accepted changes → Screen 9.

---

# Screen 9 — ATS Analysis

## Purpose
Run final job-specific quality analysis on the accepted resume version.

## Display
- ATS-oriented score
- keyword coverage
- important missing skills
- weak bullet warnings
- section completeness
- formatting/readability warnings
- role alignment

Avoid claiming compatibility with every proprietary ATS. Present the result as an ATS-oriented analysis based on transparent heuristics/model logic.

## Next
Continue → Screen 10.

---

# Screen 10 — Resume Preview

## Purpose
Allow the user to visually inspect the final derived document before export.

## Requirements
- accurate page count
- no hidden/overflowed content
- headings and bullets preserved
- page-break checks
- original formatting preserved as closely as supported

For DOCX, high-fidelity source editing is the priority.
For arbitrary PDF input, exact edit fidelity must not be promised.

## Next
Continue → Screen 11.

---

# Screen 11 — Download Options

## Actions
- Download Word (.docx)
- Download PDF
- Save to My Documents
- Create Cover Letter
- Go to Interview Prep

## Validation
Download must contain only the final user-approved content.

## Export States
- generating DOCX
- generating PDF
- ready
- rendering failed/retry

## Next
Cover Letter → Screen 12
Interview Prep → Screen 13

---

# Screen 12 — Cover Letter

## Purpose
Generate a job-specific cover letter based only on the selected job and confirmed experience.

## Field-Level AI
Each meaningful section supports AI improve/rewrite/shorten/match-JD actions.

## Non-Negotiable
Do not fabricate achievements, responsibilities or employers.

---

# Screen 13 — Interview Preparation Dashboard

## Purpose
Turn the same job + resume context into role-specific interview preparation.

## Dynamic Inputs
Questions must depend on:
- target job description
- resume technologies
- selected projects
- years of experience / seniority
- detected role expectations
- gaps identified during analysis

## Categories
- Important Technical Questions
- Scenario-Based Questions
- Project/Resume Questions
- Technology Questions
- Production Troubleshooting
- System Design
- Architecture/Trade-offs
- Coding Questions where relevant
- Behavioral Questions

Show an Interview Readiness score only if the scoring method is defined and explainable.

## Next
Select category / Start Practice → Screen 14.

---

# Screen 14 — Dynamic Question Practice

## Question Card
Display:
- question
- category
- technology
- difficulty
- priority
- why this question is relevant

## Answer Support
- Suggested answer framework
- Key points interviewer expects
- Follow-up questions
- Common mistakes
- Resume-specific context where genuinely supported

## Scenario Priority
Senior candidates should receive scenario, production, architecture and trade-off questions—not only definitions.

Example:
`Your Spring Boot service is normally 200 ms but reaches 8 seconds during peak traffic while CPU remains moderate. How do you troubleshoot it?`

## Next
Next Question / Start Mock Interview → Screen 15.

---

# Screen 15 — Mock Interview

## Flow
1. AI asks one role-specific question.
2. User answers.
3. AI evaluates against a rubric.
4. AI asks a contextual follow-up.
5. User continues until session complete.

## Evaluation Dimensions
- technical accuracy
- depth
- clarity
- production realism
- architecture reasoning where relevant
- evidence from actual experience

## Feedback
Show:
- strengths
- missing points
- improved answer framework
- what to study next

Do not create fake project stories for the candidate.

---

# Screen 16 — Application Tracker

## Purpose
Track the user's applications in the same product.

## Fields
- company
- role
- date applied
- status
- job link
- resume version used
- cover-letter version used
- notes
- next action / interview date where relevant

Typical statuses:
- Saved
- Applied
- Recruiter Contact
- Interview
- Offer
- Rejected
- Withdrawn

---

# Mobile Responsive Contract

Do not squeeze desktop layouts side-by-side.

Use step-based vertical screens, bottom sheets and sticky primary actions.

Core sequence remains identical:
Job → Resume → Analysis → Project Selection → Tailor → Review → ATS → Preview → Export → Interview Prep.

---

# Mandatory Screen Validation Gate

Every screen must be validated with this checklist before implementation is accepted:

1. Purpose is explicit.
2. Entry condition is defined.
3. Required input is defined.
4. Primary CTA is defined.
5. Disabled/hidden CTA rules are defined.
6. Validation errors are defined.
7. Loading/processing state is defined.
8. Success state is defined.
9. Failure/retry state is defined.
10. Next screen is defined.
11. Back/cancel behavior is defined.
12. Mobile behavior is defined.
13. Accessibility basics are covered.
14. No impossible state transition exists.

Examples:
- Build Resume cannot activate before both target-job input and resume parse succeed.
- Tailoring cannot alter projects that the user did not select.
- Download cannot include rejected suggestions.
- Interview questions cannot be generic when job/resume context is available.

---

# Non-Negotiable UX Rules

- Never overwrite the uploaded source document.
- Never hide AI changes from the user.
- Never fabricate work experience.
- Never falsely promise exact arbitrary-PDF format preservation.
- Preserve original DOCX formatting as a primary engineering requirement.
- Keep the path from job/resume input to downloadable output short.
- Every important resume-editing field should have contextual AI assistance.
- User approval is required before AI proposals become final resume content.

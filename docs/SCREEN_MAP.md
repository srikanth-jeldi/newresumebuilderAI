# Resumaire — Complete Screen Map

Brand: **Resumaire — An EpitomeHub® Product**

This file extends the UX contract with authenticated workspace, template gallery, saved resume/version management, ATS history and career workspace flows.

## Top-Level Product Flow

```text
PUBLIC
1. Landing
2. Login / Register / Forgot Password

AUTHENTICATED WORKSPACE
3. Dashboard
4. My Resumes
5. Resume Detail / Version History
6. Template Gallery

CREATE FLOW
7. Create Resume — Template Selection
8. Personal Information
9. Professional Summary
10. Work Experience / Projects
11. Education
12. Skills
13. Certifications / Additional Sections
14. Resume Editor + Live Preview + Field-Level AI
15. ATS Analysis (when JD exists)
16. Preview
17. Save Version / Download DOCX / PDF

OPTIMIZE FLOW
18. Paste / Upload Job Description
19. Upload Existing Resume
20. AI Match Analysis
21. Select Projects to Tailor
22. Build Resume or Customize Resume
23. AI Proposal Review
24. Resume Editor + Field-Level AI
25. Final ATS Analysis
26. Preview
27. Save as New Resume Version
28. Download DOCX / PDF

CAREER FLOW
29. Cover Letters
30. Interview Preparation Dashboard
31. Dynamic Question Practice
32. Mock Interview
33. Interview Session Results / Study Gaps
34. Application Tracker
35. Application Detail

ACCOUNT
36. Profile / Settings
37. Subscription / Usage if commercial model is enabled
```

## Screen 2 — Login / Registration

Required capabilities:
- email/password registration
- login
- logout
- forgot/reset password
- secure session handling
- optional social login only if architecture explicitly approves it

After successful login:
→ Dashboard

Unauthenticated users who begin a create/optimize flow may be asked to authenticate before persistent saving/export according to product policy, but the UX must not unexpectedly discard their work.

## Screen 3 — Dashboard

Primary cards/sections:
- Create New Resume
- Optimize Existing Resume
- Paste Job
- My Resumes
- Recent Resumes
- Recent ATS Scores
- Cover Letters
- Interview Prep
- Applications

Each recent resume card can show:
- title
- target role/company
- template thumbnail
- latest ATS score if available
- last modified
- status

## Screen 4 — My Resumes

Show all user-owned resume families and versions.

Filters/sorting where useful:
- recently modified
- created from template
- uploaded original
- tailored
- target role/company

Card/list actions:
- Open
- Edit
- Duplicate
- Rename
- Tailor for Job
- Run ATS Check
- Create Cover Letter
- Interview Prep
- Download
- Archive/Delete

Never expose another user's resume by changing an ID.

## Screen 5 — Resume Detail / Version History

Display:
- immutable original source where applicable
- all derived versions
- creation/modification time
- job/company context
- template
- ATS score per analyzed version
- change summary

Actions:
- restore/duplicate an older version into a new editable version
- compare versions where practical
- download a chosen version

Never overwrite historical versions in-place when the operation is meant to preserve history.

## Screen 6 — Template Gallery

Provide many original Resumaire templates with comparable breadth to established resume-builder products.

Filters/tags may include:
- ATS friendly
- modern
- professional
- executive
- simple
- creative
- one-column
- two-column
- entry-level
- experienced

Each template:
- thumbnail
- preview
- Select Template

Do not copy proprietary third-party templates pixel-for-pixel.

## Create-Resume Editor Contract

Every meaningful text field should expose contextual AI assistance where relevant.

Actions can include:
- AI Suggest
- Improve
- Rewrite
- Shorten
- Expand
- Strengthen Action Verb
- Quantify Impact
- Match Job Description
- ATS Keyword Alignment
- Fix Grammar

AI output must appear as a proposal and never silently replace user-entered text.

## ATS Score Placement

ATS-oriented scoring may appear in:
- optimize-flow analysis screen
- resume editor side panel
- final ATS analysis screen
- My Resumes card metadata
- Resume Detail / Version History

Do not show a score where no relevant job description exists unless the product defines a separate generic resume-quality score. Keep those two concepts distinct.

## Persistence Rules

For an authenticated user persist, subject to retention/product policy:
- uploaded source resumes
- parsed structured resume data
- resume families
- resume versions
- selected template and styling configuration
- ATS analysis results/history
- target job descriptions used for tailoring
- cover-letter versions
- interview-prep sessions/results as product scope requires
- application tracker records

Ownership must be enforced server-side for every private resource.

## Navigation Principles

Global authenticated navigation should provide clear access to:
- Dashboard
- My Resumes
- Templates
- Cover Letters
- Interview Prep
- Applications
- Career Tools
- Profile

The optimize/create flows may use focused steppers, but users must be able to safely save progress and return later.

## Validation Gate

Every screen must define:
1. purpose
2. entry condition
3. required data
4. primary CTA
5. validation rules
6. loading state
7. empty state
8. success state
9. error/retry state
10. next screen
11. back/cancel behavior
12. persistence/save behavior
13. authorization requirements
14. responsive/mobile behavior
15. accessibility basics

A screen is not architecture-ready until these states are defined.

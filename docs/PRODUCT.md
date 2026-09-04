# Resumaire — Product Definition

Brand: **Resumaire — An EpitomeHub® Product**

## Product Goal
Resumaire is an authenticated AI career workspace, not only a one-time resume editor.

It should combine the familiar capabilities users expect from leading resume-builder products with Resumaire-specific AI features:
- create resumes from scratch
- upload and tailor existing resumes
- preserve uploaded DOCX formatting where technically possible
- offer many professional resume templates
- provide field-level AI suggestions throughout resume creation
- compute transparent ATS-oriented job-match scores
- store original resumes and every generated/customized version for the logged-in user
- generate cover letters
- prepare the user for interviews with dynamic role/resume/technology-specific questions
- run mock interviews
- track job applications

The strongest differentiator is the end-to-end flow from job description -> resume tailoring -> ATS analysis -> interview preparation -> application tracking while using only truthful/confirmed user experience.

## Primary Users
- Job seekers creating a first resume
- Experienced professionals tailoring an existing resume for a specific job
- Users maintaining different resume versions for different roles
- Users who want ATS-oriented feedback
- Users who want role-specific interview preparation from the same resume/JD context

## Authentication and User Workspace
Users must be able to:
- register
- login
- logout
- recover/reset password
- maintain a secure session
- manage profile/account settings

After login, land on a personal dashboard.

The logged-in workspace stores only resources owned by that user.

### My Resumes
Show:
- original uploaded resumes
- resumes created from templates
- tailored/modified versions
- draft resumes
- final/exported versions

For each resume/version show useful metadata such as:
- title
- target role/company where applicable
- source resume
- template
- latest ATS-oriented score where available
- last modified date
- status

Actions should include where relevant:
- open/edit
- duplicate
- rename
- tailor for another job
- run ATS analysis
- create cover letter
- prepare for interview
- download DOCX/PDF
- archive/delete
- inspect version history

Original uploaded source files remain immutable. Modified resumes are versioned derivatives.

## Resume Templates
Resumaire must provide a broad template gallery similar in breadth to established resume-builder products but with original Resumaire designs and assets.

Template capabilities:
- many professional designs
- ATS-friendly options clearly identified
- filters by style/role/experience level where useful
- template preview
- change template without losing resume content
- colors/fonts/spacing controls where supported
- responsive preview
- DOCX/PDF export

Do not copy third-party proprietary templates pixel-for-pixel. Build original templates with comparable usability and quality.

## Core Product Flows

### Flow A — Create New Resume
1. Login/create account if required by product policy.
2. Choose a template or start with a recommended template.
3. Enter personal/contact information.
4. Add professional summary.
5. Add work experience/projects.
6. Add education.
7. Add skills.
8. Add certifications/achievements/custom sections.
9. Use contextual AI suggestions at meaningful text fields.
10. Preview.
11. Run ATS-oriented analysis if a job description is supplied.
12. Save to My Resumes.
13. Download DOCX/PDF.

### Flow B — Optimize Existing Resume for a Job
1. Paste/upload target job description or responsibilities.
2. Upload existing resume.
3. Parse resume and detect sections/projects.
4. Analyze job vs resume.
5. Show ATS-oriented match score, strengths, gaps and suggestions.
6. Ask which projects/experience blocks should be tailored:
   - latest project
   - latest 2 projects
   - latest N projects
   - manual selection
   - all projects
7. Choose Build Resume or Customize Resume.
8. Review AI proposals.
9. Accept/reject/edit suggestions.
10. Run final ATS-oriented analysis.
11. Preview.
12. Save as a new version in My Resumes.
13. Download DOCX/PDF.
14. Continue to Cover Letter / Interview Prep / Application Tracker.

## Field-Level AI Assistance
Every meaningful editable text area should offer contextual AI actions where appropriate.

Examples:
- AI Suggest
- Improve
- Rewrite
- Shorten
- Expand
- strengthen action verbs
- improve clarity
- fix grammar
- quantify impact
- match target job
- ATS keyword alignment

Target areas include:
- professional headline
- summary
- work-experience bullets
- project descriptions
- achievements
- skills descriptions
- education descriptions where useful
- certifications/custom sections
- cover-letter fields

AI must always produce visible proposals. It must not silently overwrite user text.

## ATS-Oriented Analysis
Resumaire should provide job-specific, explainable ATS-oriented scoring and guidance.

Display where relevant:
- overall match score
- skills match
- experience alignment
- keyword coverage
- section completeness
- role/seniority alignment
- formatting/readability warnings
- strong areas
- missing/weakly represented requirements
- actionable suggestions

Do not claim guaranteed acceptance by every proprietary ATS. Scoring must be positioned as Resumaire's transparent ATS-oriented analysis.

Store ATS analysis history per resume version where useful so the user can compare improvement over time.

## Document-First Tailoring
For users with existing resumes, Resumaire should preserve the user's original format as much as technically possible.

### DOCX
Preferred editable source.

High-fidelity goal:
- page setup
- fonts
- paragraph styles
- headings
- tables
- spacing
- headers/footers where supported
- section order
- overall visual structure

Prefer editing the existing OOXML structure rather than rebuilding from a generic template when fidelity mode is possible.

### PDF
Accepted for reading/import, but exact in-place editable layout preservation cannot be guaranteed for arbitrary PDFs.

For PDF uploads:
- extract text/layout where possible
- detect sections/projects
- provide best-effort editable reconstruction
- clearly flag fidelity limitations
- recommend original DOCX for closest format preservation

## Experience-Integrity Rule
Job-description requirements are not proof that a user performed those responsibilities.

Resumaire may:
- improve wording of existing work
- map equivalent skills/activities
- reorganize truthful bullets
- suggest missing responsibilities for explicit confirmation

Resumaire must not silently fabricate:
- technologies
- employers
- responsibilities
- achievements
- metrics
- project experience

## Cover Letters
Generate job-specific cover letters from the selected job and confirmed resume/profile data.

Allow field-level AI editing and save cover-letter versions to the user's workspace.

## Interview Preparation
Use the same job description + resume + selected projects + experience level to dynamically generate interview preparation.

Categories should include:
- important technical questions
- scenario-based questions
- project/resume questions
- technology-specific questions
- production troubleshooting
- system design
- architecture/trade-offs
- coding where relevant
- behavioral questions

For senior profiles, prioritize scenario, production, architecture and trade-off questions over basic definitions.

Show why each question is relevant when useful.

## Mock Interview
AI should:
1. ask a role-specific question
2. evaluate the user's answer against a rubric
3. identify strengths and missing points
4. ask a contextual follow-up
5. provide an improved answer framework without inventing fake experience

## Application Tracker
Authenticated users can track:
- company
- role
- job link
- date applied
- status
- resume version used
- cover-letter version used
- notes
- next action/interview date

Typical statuses:
- Saved
- Applied
- Recruiter Contact
- Interview
- Offer
- Rejected
- Withdrawn

## User Dashboard
After login show a useful dashboard containing:
- My Resumes
- Recent resume versions
- ATS scores/improvement
- Cover Letters
- Interview Prep sessions
- Applications
- quick actions: Create Resume / Optimize Resume / Paste Job

## Platforms
Initial priority:
- responsive web application

Later, using the same backend contracts:
- Android via Flutter
- iOS via Flutter

## Product Principles
- Resumaire has its own brand/design; third-party sites are only product-pattern references.
- User data is private and ownership-checked server-side.
- Original uploaded resumes remain immutable.
- Derived revisions are versioned and recoverable.
- AI changes are visible and reversible.
- AI must not fabricate professional experience.
- Export quality is a core feature.
- Template variety is a core product capability, not an afterthought.
- Login should unlock a persistent career workspace rather than a disposable session.

## Initial Success Criteria
- User can register/login and see a private dashboard.
- User can create multiple resumes from original Resumaire templates.
- User can upload an existing resume and tailor selected projects against a JD.
- AI help is available throughout resume editing.
- ATS-oriented score and actionable breakdown are available per relevant resume version.
- Original and modified resume versions are stored and recoverable.
- User can preview and export DOCX/PDF.
- User can generate a cover letter from the same job context.
- User can enter dynamic interview preparation and mock interview flows.
- User can track job applications and associate them with a resume version.
- Cross-user access to resumes/documents/analyses is blocked.

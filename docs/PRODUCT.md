# New Resume Builder AI — Product Definition

## Product Goal
Build a resume-creation platform that helps users create, improve, tailor and export professional resumes across web and mobile, with AI assistance that remains user-controlled.

## Primary Users
- Job seekers creating a first resume
- Experienced professionals maintaining multiple resume variants
- Users tailoring a resume for a specific job description
- Users who need ATS-oriented guidance and export-ready documents

## MVP Outcomes
A user should be able to:
1. Register/login securely.
2. Create multiple resumes.
3. Add/edit/reorder core resume sections.
4. Choose a supported template.
5. Preview the resume.
6. Export a valid PDF.
7. Request AI writing improvements without silent overwrite.
8. Compare a resume with a job description and receive structured ATS-oriented feedback.
9. Access owned resumes from web and mobile.

## MVP Core Sections
- Personal/contact information
- Professional summary
- Work experience
- Education
- Skills
- Projects
- Certifications

Custom sections and advanced template customization can follow after MVP unless architecture can support them without adding delivery complexity.

## AI Features
MVP candidates:
- Rewrite/improve selected text
- Summary suggestions
- Bullet-point improvements
- Job-description keyword/skill matching
- ATS-oriented analysis and suggestions

AI suggestions are drafts. User content must not be silently replaced.

## Platforms
- Web
- Android via Flutter
- iOS via Flutter

## Product Principles
- User owns and controls resume content.
- Private resume data must remain private.
- Export quality is a core feature, not an afterthought.
- Web/mobile should share backend contracts.
- AI should enhance deterministic product behavior, not replace authorization, validation or persistence rules.

## Out of Scope for Initial Architecture
Unless explicitly promoted into MVP:
- Marketplace/recruiter network
- Enterprise ATS platform
- Social network features
- Complex real-time collaboration
- Premature multi-region/microservice infrastructure

## Initial Success Criteria
- Core resume flow works end-to-end.
- PDF content is complete and readable.
- Cross-user resource access is blocked.
- Web/mobile consume consistent APIs.
- AI errors degrade gracefully without losing user work.

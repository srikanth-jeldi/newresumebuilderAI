# Resume Builder AI — UX Flow

Status: MVP DESIGN CONTRACT

## Design Direction
Use the supplied Resume-Now screenshot only as a visual-layout reference for hierarchy, whitespace, premium resume presentation and strong CTA placement.

Do not copy Resume-Now branding, logo, proprietary illustrations, reviews, wording, or exact visual assets.

The product should have its own brand identity while keeping a similarly clean, high-conversion resume-builder experience.

## Desktop Landing Page
### Header
Left:
- New Resume Builder AI logo/brand

Right:
- How it works
- Contact
- Login / Account

Keep the header lightweight and spacious.

## Hero Layout
Use a two-column hero on desktop.

### Left Column — Main Workflow Input
Headline example:
`Tailor your resume to the job in minutes.`

Supporting copy:
`Paste the role requirements, upload your current resume, choose the projects to update, and download a tailored Word or PDF version.`

### Card 1 — Role & Responsibilities
Large textarea:
- placeholder: `Paste job description, role or responsibilities here...`

Secondary upload control:
- drag/drop zone
- `Browse document`
- TXT / DOCX / PDF

State indicator:
- Empty
- Parsing
- Ready
- Error

### Card 2 — Resume Upload
Initially visible but secondary; becomes visually emphasized after target requirements are ready.

Controls:
- Drag/drop
- `Browse resume`
- DOCX recommended
- PDF accepted with fidelity note

Fidelity microcopy:
`For the closest formatting match, upload the original Word (.docx) file.`

### CTA State Logic
Before both inputs are complete:
- primary CTA disabled or hidden

After requirements + resume are ready:
Show two strong actions:

1. `Build Resume`
2. `Customize Resume`

`Build Resume` is the primary filled CTA.
`Customize Resume` is secondary outlined CTA.

## Right Column — Product Visual
Display an original resume preview mockup showing:
- current resume
- highlighted suggested bullet
- AI suggestion chip
- before/after indication
- project selection badge

Do not use the reference site's resume/person images.

## Project Selection Step
After parsing, open an inline panel/modal/stepper:

Heading:
`Which projects should we tailor?`

Choices:
- Latest project
- Latest 2 projects
- Latest N projects
- Choose projects manually

For `Latest N`:
- numeric selector bounded by detected project count

For manual selection:
- project cards with title/company/date/role
- checkbox/toggle

If the parser is uncertain:
`We found these experience/project sections. Confirm them before we update your resume.`

User must be able to correct project boundaries/titles before generation.

## Build Resume Flow
1. Analyze requirements.
2. Match against selected project content.
3. Generate structured proposal set.
4. Classify each proposal:
   - supported rewrite
   - supported keyword/skill alignment
   - requires confirmation
   - unsupported / do not add
5. Show concise review summary.
6. User confirms build.
7. Generate derived resume version.
8. Show document preview.
9. Enable DOCX/PDF download.

## Customize Resume Flow
Use a split layout on desktop:

Left:
- detected sections/projects
- suggestion list
- accepted/rejected/pending state

Right:
- live resume preview

Actions per suggestion:
- Accept
- Reject
- Edit
- Move to another selected project

Global actions:
- Apply accepted changes
- Undo last change
- Reset to uploaded source
- Download Word
- Download PDF

## Mobile Responsive Flow
Do not squeeze two desktop columns side-by-side.

Use vertical steps:
1. Requirements
2. Resume upload
3. Project selection
4. Suggestions
5. Preview/export

Use sticky bottom CTA when a step is actionable.

## Visual Principles
- generous whitespace
- large bold hero typography
- rounded but professional cards
- strong primary CTA
- subtle borders/shadows
- resume preview as the hero visual
- minimal navigation
- accessible contrast
- clear progress states
- no fake Trustpilot/review numbers unless real verified data exists

## Required Screen States
### Requirements input
- empty
- text entered
- upload parsing
- upload parsed
- unsupported file
- parse failure

### Resume upload
- empty
- uploading
- parsing
- parsed
- low-confidence project detection
- unsupported file
- encrypted/password-protected file
- parse failure

### Generation
- matching
- generating proposals
- confirmation needed
- build ready
- provider unavailable

### Export
- generating DOCX
- generating PDF
- ready
- rendering failed with retry

## Non-Negotiable UX Rules
- Never overwrite the uploaded source document.
- Never hide AI changes from the user.
- Never claim exact PDF format preservation when it cannot be guaranteed.
- Never fabricate a work responsibility without user confirmation.
- Keep the path from upload to downloadable output as short as possible.

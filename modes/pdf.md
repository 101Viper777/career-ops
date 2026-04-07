# Mode: pdf - ATS-Optimised PDF Generation

## Full pipeline

1. Read `cv.md` as source of truth.
2. Ask for JD if not already in context (text or URL).
3. Extract 15-20 JD keywords.
4. Detect JD language and match CV language (English default).
5. Detect company location and pick paper format:
   - US/Canada -> `letter`
   - others -> `a4`
6. Detect role archetype and adapt framing.
7. Rewrite Professional Summary with JD keywords plus exit narrative bridge.
8. Select top 3-4 most relevant projects.
9. Reorder experience bullets by JD relevance.
10. Build competency grid from JD requirements (6-8 phrases).
11. Inject keywords naturally into existing evidence only (never invent).
12. Generate complete HTML from template + tailored content.
13. Write HTML to `/tmp/cv-candidate-{company}.html`.
14. Run:
   `node generate-pdf.mjs /tmp/cv-candidate-{company}.html output/cv-candidate-{company}-{YYYY-MM-DD}.pdf --format={letter|a4}`
15. Report output path, page count, and keyword coverage estimate.

## ATS rules

- single-column layout only
- standard headers: Professional Summary, Work Experience, Education, Skills, Certifications, Projects
- no critical text inside images/SVGs
- no critical info in PDF header/footer
- UTF-8 selectable text (not rasterised)
- no nested tables
- distribute JD keywords across summary, first bullet per role, and skills

## PDF design

- fonts: Space Grotesk (headings 600-700) + DM Sans (body 400-500)
- self-hosted fonts from `fonts/`
- header: name 24px bold + 2px gradient line + contact row
- section headers: Space Grotesk 13px uppercase, letter-spacing 0.05em
- body: DM Sans 11px, line-height 1.5
- company names: accent purple
- margins: 0.6in
- background: white

## Section order (6-second recruiter scan)

1. Header
2. Professional Summary
3. Core Competencies
4. Work Experience
5. Projects (top 3-4 relevant)
6. Education and Certifications
7. Skills

## Ethical keyword injection strategy

Legitimate reframing examples:
- JD says "RAG pipelines", CV says "LLM workflows with retrieval" -> "RAG pipeline design and LLM orchestration workflows"
- JD says "MLOps", CV says "observability, evals, error handling" -> "MLOps and observability: evals, error handling, cost monitoring"
- JD says "stakeholder management", CV says "collaborated with team" -> "stakeholder management across engineering, operations, and business"

Never add skills the candidate does not have.

## HTML template

Use `cv-template.html` and replace `{{...}}` placeholders with tailored content:

| Placeholder | Content |
| --- | --- |
| `{{LANG}}` | `en` or `es` |
| `{{PAGE_WIDTH}}` | `8.5in` (letter) or `210mm` (A4) |
| `{{NAME}}` | from profile |
| `{{EMAIL}}` | from profile |
| `{{LINKEDIN_URL}}` | from profile |
| `{{LINKEDIN_DISPLAY}}` | from profile |
| `{{PORTFOLIO_URL}}` | from profile |
| `{{PORTFOLIO_DISPLAY}}` | from profile |
| `{{LOCATION}}` | from profile |
| `{{SECTION_SUMMARY}}` | section label |
| `{{SUMMARY_TEXT}}` | tailored summary |
| `{{SECTION_COMPETENCIES}}` | section label |
| `{{COMPETENCIES}}` | competency tags |
| `{{SECTION_EXPERIENCE}}` | section label |
| `{{EXPERIENCE}}` | experience HTML |
| `{{SECTION_PROJECTS}}` | section label |
| `{{PROJECTS}}` | projects HTML |
| `{{SECTION_EDUCATION}}` | section label |
| `{{EDUCATION}}` | education HTML |
| `{{SECTION_CERTIFICATIONS}}` | section label |
| `{{CERTIFICATIONS}}` | certifications HTML |
| `{{SECTION_SKILLS}}` | section label |
| `{{SKILLS}}` | skills HTML |

## Post-generation

If the offer already exists in tracker, update PDF status from `❌` to `✅`.
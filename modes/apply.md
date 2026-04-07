# Mode: apply - Live Application Assistant

Interactive mode for when the candidate is filling out a job application form in Chrome. Read the visible form, load the existing offer context, and generate tailored answers for each field.

## Requirements

- **Best with visible Playwright**: the candidate can see the browser while Claude interacts with the page.
- **Without Playwright**: the candidate shares screenshots or pastes form questions manually.

## Workflow

```
1. DETECT    -> Read active browser tab (screenshot/URL/title)
2. IDENTIFY  -> Extract company and role from page
3. SEARCH    -> Match against existing reports in reports/
4. LOAD      -> Read full report and Section G if present
5. COMPARE   -> Check if on-page role matches evaluated role
6. ANALYSE   -> Identify ALL visible form questions
7. GENERATE  -> Produce tailored answer for each question
8. PRESENT   -> Return formatted copy-paste answers
```

## Step 1 - Detect the offer

**With Playwright:** take a snapshot of the active page. Read title, URL, and visible content.

**Without Playwright:** ask the candidate to:

- share a screenshot of the form (Read tool supports images)
- paste the questions as text
- provide company and role so we can search reports

## Step 2 - Identify and load context

1. Extract company name and role title from the page
2. Search `reports/` by company name (case-insensitive)
3. If a match exists, load the full report
4. If Section G exists, use draft answers as baseline
5. If no match exists, warn and offer quick auto-pipeline evaluation

## Step 3 - Detect role changes

If the role on screen differs from the evaluated report:

- warn candidate: "The role changed from [X] to [Y]. Re-evaluate or adapt existing answers?"
- if adapt: tailor responses to new title without full re-evaluation
- if re-evaluate: run full A-F evaluation, update report, regenerate Section G
- update tracker role title in `data/applications.md` if required

## Step 4 - Analyse form questions

Identify ALL visible questions:

- free-text fields (cover letter, why this role, etc.)
- dropdowns (source, work authorisation, etc.)
- yes/no fields (relocation, visa, etc.)
- salary fields (range, expectations)
- upload fields (resume, cover letter PDF)

Classify each question:

- already answered in Section G -> adapt existing draft
- new question -> generate from report + `cv.md`

## Step 5 - Generate answers

For each question:

1. Use report evidence from section B and STAR stories from section F
2. Reuse Section G draft answers when available
3. Keep "I'm choosing you" tone from auto-pipeline
4. Reference one concrete JD detail visible on the page
5. Include one career-ops proof point in "Additional information" fields where relevant
6. Keep a personal voice that sounds written by the candidate, not AI
7. Avoid em dashes, filler lines, and repetitive sentence shapes
8. Keep responses to 2-4 sentences, one idea per sentence

**Output format:**

```
## Answers for [Company] - [Role]

Based on: Report #NNN | Score: X.X/5 | Archetype: [type]

---

### 1. [Exact form question]
> [Copy-paste answer]

### 2. [Next question]
> [Answer]

...

---

Notes:
- [Any role change or assumptions]
- [Suggested final personal edits before submit]
```

## Step 6 - Post-apply (optional)

If the candidate confirms they submitted:

1. Update status in `data/applications.md` from `Evaluated` to `Applied`
2. Update report Section G with final submitted answers
3. Suggest next step: `/career-ops contacto` for LinkedIn outreach

## Scroll handling

If the form has more questions than currently visible:

- ask candidate to scroll and share another screenshot
- or paste remaining questions
- process iteratively until all fields are covered


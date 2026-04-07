# Mode: auto-pipeline - Full Automatic Pipeline

When the user pastes a JD (text or URL) without an explicit sub-command, run the entire pipeline in sequence.

## Step 0 - Extract JD

If the input is a **URL** (not pasted JD text), use this extraction strategy:

**Priority order:**

1. **Playwright (preferred):** most job portals (Lever, Ashby, Greenhouse, Workday) are SPAs. Use `browser_navigate` + `browser_snapshot`.
2. **WebFetch (fallback):** for static pages (ZipRecruiter, WeLoveProduct, company careers pages).
3. **WebSearch (last resort):** search role title + company in mirror/index pages with static HTML.

If no method works, ask candidate to paste JD text or share a screenshot.

If input is pasted JD text, use it directly.

## Step 1 - A-F evaluation

Run exactly as mode `oferta` (`modes/oferta.md`) for all A-F blocks.

## Step 2 - Save report markdown

Save full evaluation to `reports/{###}-{company-slug}-{YYYY-MM-DD}.md` (see format in `modes/oferta.md`).

## Step 3 - Generate PDF

Run full `pdf` pipeline (`modes/pdf.md`).

## Step 4 - Draft application answers (only if score >= 4.5)

If final score is >= 4.5, generate draft application answers:

1. Extract form questions with Playwright snapshot. If extraction fails, use generic question set.
2. Generate answers using tone rules below.
3. Save into report as `## G) Draft Application Answers`.

### Generic questions (fallback set)

- Why are you interested in this role?
- Why do you want to work at [Company]?
- Tell us about a relevant project or achievement.
- What makes you a good fit for this position?
- How did you hear about this role?

### Tone rules for form answers

**Positioning:** "I'm choosing you." The candidate has options and is selecting this company for specific reasons.

**Tone guidelines:**

- **Confident, not arrogant:** show earned confidence from real delivery
- **Selective, not dismissive:** explain why this role is the right fit now
- **Specific and concrete:** cite one real JD detail and one real candidate proof point
- **Direct, no fluff:** 2-4 sentences per answer
- **Proof over claims:** replace "I'm great at X" with "I built X that delivered Y"
- **Human voice, not template:** natural wording, no over-polished corporate language
- **No em dashes:** use commas, full stops, or hyphens only
- **One idea per sentence:** short, clear, easy to read

**Framework by question:**

- **Why this role?** "Your [specific need] maps to [specific work I have done]."
- **Why this company?** mention one concrete reason tied to company context.
- **Relevant experience?** use one quantified or concrete proof point.
- **Good fit?** explain intersection of role demands and proven strengths.
- **How did you hear?** answer honestly (portal, scan, referral, etc.).

**Language rule:** generate in JD language (English by default).

## Step 5 - Update tracker

Register in `data/applications.md` with all fields, including report link and PDF status.

If any step fails, continue pipeline and mark failed step as pending in tracker notes.
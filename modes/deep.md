# Mode: deep - Deep Research Prompt

Generate a structured prompt for Perplexity, Claude, or ChatGPT using 6 axes:

```markdown
## Deep Research: [Company] - [Role]

Context: I am evaluating an application for [role] at [company]. I need practical interview intelligence.

### 1. AI Strategy
- Which products/features use AI/ML?
- What is their AI stack (models, infrastructure, tools)?
- Do they publish engineering blogs?
- Any papers or talks on AI?

### 2. Recent Moves (last 6 months)
- Key hires in AI/ML/product?
- Acquisitions or partnerships?
- Product launches or pivots?
- Funding rounds or leadership changes?

### 3. Engineering Culture
- Delivery cadence and CI/CD maturity?
- Monorepo or multi-repo?
- Main languages/frameworks?
- Remote-first or office-first?
- Glassdoor/Blind signals on engineering culture?

### 4. Likely Challenges
- Scaling bottlenecks?
- Reliability, cost, or latency constraints?
- Ongoing migrations (infra, models, platforms)?
- Recurring pain points from public reviews?

### 5. Competitors and Differentiation
- Main competitors?
- Core moat or differentiator?
- How they position against competition?

### 6. Candidate Angle
Given my profile (read from cv.md and profile.yml):
- What unique value can I bring to this team?
- Which of my projects are most relevant?
- Which interview story should I lead with?
```

Customise each section with offer-specific context.

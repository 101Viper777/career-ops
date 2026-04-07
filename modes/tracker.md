# Mode: tracker - Application Tracker

Read and display `data/applications.md`.

**Tracker format:**

```markdown
| # | Date | Company | Role | Score | Status | PDF | Report |
```

Canonical statuses:
`Evaluated` -> `Applied` -> `Responded` -> `Interview` -> `Offer` / `Rejected` / `Discarded` / `SKIP`

Status definitions:

- `Applied`: candidate submitted an application
- `Responded`: recruiter or company replied and candidate responded
- `Interview`: candidate is in interview process

If user asks to update a status, edit the matching row.

Also show summary stats:

- total applications
- applications by status
- average score
- percentage with PDF generated
- percentage with report generated


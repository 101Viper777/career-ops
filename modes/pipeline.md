# Mode: pipeline - URL Inbox Processing

Process accumulated job URLs from `data/pipeline.md`. User can add URLs any time, then run `/career-ops pipeline`.

## Workflow

1. Read `data/pipeline.md` and find `- [ ]` items under "Pending".
2. For each pending URL:
   a. Calculate next sequential `REPORT_NUM` from `reports/`
   b. Extract JD via Playwright (`browser_navigate` + `browser_snapshot`), then WebFetch, then WebSearch
   c. If URL is inaccessible, mark `- [!]` with note and continue
   d. Run full auto-pipeline: A-F evaluation -> report markdown -> PDF (if score >= 3.0) -> tracker update
   e. Move item from "Pending" to "Processed" as:
      `- [x] #NNN | URL | Company | Role | Score/5 | PDF ✅/❌`
3. If there are 3+ pending URLs, run subagents in parallel (`run_in_background`) when safe.
4. At completion, show summary table:

```markdown
| # | Company | Role | Score | PDF | Recommended action |
```

## `pipeline.md` format

```markdown
## Pending
- [ ] https://jobs.example.com/posting/123
- [ ] https://boards.greenhouse.io/company/jobs/456 | Company Inc | Senior PM
- [!] https://private.url/job - Error: login required

## Processed
- [x] #143 | https://jobs.example.com/posting/789 | Acme Corp | AI PM | 4.2/5 | PDF ✅
- [x] #144 | https://boards.greenhouse.io/xyz/jobs/012 | BigCo | SA | 2.1/5 | PDF ❌
```

## Smart JD extraction by URL type

1. Playwright preferred for SPAs.
2. WebFetch fallback for static pages.
3. WebSearch last resort for indexed mirrors.

Special cases:
- LinkedIn may require login: mark `[!]` and ask user to paste JD text
- PDF URL: read directly with Read tool
- `local:` prefix: read local file, e.g. `local:jds/linkedin-pm-ai.md`

## Report numbering

1. List files in `reports/`
2. Extract numeric prefix from each report filename
3. New number equals max plus 1

## Source sync check

Before processing any URL, run:
```bash
node cv-sync-check.mjs
```
If sync warnings appear, notify user before continuing.

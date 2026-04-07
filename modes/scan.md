# Mode: scan - Portal Scanner

Scan configured job portals, filter by role relevance, and add new offers to pipeline.

## Recommended execution

Run as background subagent to preserve main context:

```python
Agent(
  subagent_type="general-purpose",
  prompt="[this mode file + runtime data]",
  run_in_background=True
)
```

## Configuration inputs

Read `portals.yml`:

- `search_queries`: WebSearch queries with `site:` filters
- `tracked_companies`: direct company career pages via `careers_url`
- `title_filter`: `positive`, `negative`, `seniority_boost`

Read dedup sources:

- `data/scan-history.tsv`
- `data/applications.md`
- `data/pipeline.md`

## Discovery strategy (3 levels)

### Level 1 - Direct Playwright scan (primary)

For each enabled company in `tracked_companies` with `careers_url`:

1. `browser_navigate` to careers URL
2. `browser_snapshot` and extract all visible job listings
3. handle paging/sections when needed
4. capture `{title, url, company}`
5. if careers URL fails, use `scan_query` fallback and flag URL for maintenance

### Level 2 - Greenhouse API (complementary)

For enabled companies with `api` URL:

1. fetch API JSON
2. extract `{title, url, company}`
3. merge with level 1 results

### Level 3 - WebSearch (broad discovery)

For enabled `search_queries`:

1. run WebSearch query
2. parse `{title, url, company}` from results
3. merge with level 1 and level 2 candidates

Execution priority:

1. level 1, 2) level 2, 3) level 3

All levels are additive. Merge and deduplicate at the end.

## Filtering and dedup

Apply title filtering rules:

- at least one `positive` keyword
- zero `negative` keywords
- `seniority_boost` increases ranking but is optional

Deduplicate against:

- exact URL in `scan-history.tsv`
- normalised company + role in `applications.md`
- exact URL in `pipeline.md`

## Outputs

For each new role:

1. append to `pipeline.md` pending:
  `- [ ] {url} | {company} | {title}`
2. append to `scan-history.tsv`:
  `{url}\t{date}\t{source}\t{title}\t{company}\tadded`

For filtered roles:

- write `skipped_title` to scan history

For duplicates:

- write `skipped_dup` to scan history

## WebSearch parsing notes

Common result title patterns:

- `Job Title @ Company`
- `Job Title | Company`
- `Job Title - Company`
- `Job Title at Company`

Use robust parsing and fallback to domain-based company extraction when needed.

## Private URL handling

If URL is not publicly accessible:

1. save JD to `jds/{company}-{role-slug}.md`
2. add `local:` entry in pipeline:
  `- [ ] local:jds/{company}-{role-slug}.md | {company} | {title}`

## Output summary format

```text
Portal Scan - {YYYY-MM-DD}
Queries executed: N
Offers found: N
Relevant after filter: N
Duplicates: N
Added to pipeline: N

+ {company} | {title} | {source}
...

Next: run /career-ops pipeline
```

## `careers_url` maintenance

Each tracked company should include `careers_url`.

If missing:

1. infer from known ATS pattern (Ashby, Greenhouse, Lever)
2. fallback WebSearch: `"{company}" careers jobs`
3. verify with Playwright
4. save in `portals.yml`

If careers URL breaks:

1. note issue in summary
2. use `scan_query` fallback
3. flag for manual update


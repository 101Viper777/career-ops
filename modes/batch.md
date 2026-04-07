# Mode: batch - Bulk Offer Processing

Two usage modes:

- **conductor with Chrome** for live portal navigation
- **standalone script** for pre-collected URLs

## Architecture

```text
Conductor (claude --chrome)
  -> navigates portal in logged-in browser
  -> reads DOM in real time
  -> dispatches one worker per offer
  -> merges tracker additions at end
```

Each worker is an isolated `claude -p` process with fresh context. Conductor orchestrates only.

## Files

```text
batch/
  batch-input.tsv
  batch-state.tsv
  batch-runner.sh
  batch-prompt.md
  logs/
  tracker-additions/
```

## Mode A: Conductor with Chrome

1. Read `batch/batch-state.tsv` to resume progress.
2. Navigate portal and collect offer URLs into `batch-input.tsv`.
3. For each pending URL:
  - click offer and read JD from DOM
  - save JD to `/tmp/batch-jd-{id}.txt`
  - compute next report number
  - run worker:
    ```bash
    claude -p --dangerously-skip-permissions \
      --append-system-prompt-file batch/batch-prompt.md \
      "Process offer URL:{url} JD:/tmp/batch-jd-{id}.txt Report:{num} ID:{id}"
    ```
  - update `batch-state.tsv` with status, score, report number
  - write log to `batch/logs/{report_num}-{id}.log`
4. Move to next result/page until done.
5. Merge `tracker-additions/` into `applications.md` and report summary.

## Mode B: Standalone script

```bash
batch/batch-runner.sh [OPTIONS]
```

Options:

- `--dry-run`: list pending only
- `--retry-failed`: retry failed entries only
- `--start-from N`: start from entry id N
- `--parallel N`: run N workers in parallel
- `--max-retries N`: retry limit per offer (default 2)

## `batch-state.tsv` format

```text
id	url	status	started_at	completed_at	report_num	score	error	retries
1	https://...	completed	...	...	002	4.2	-	0
2	https://...	failed	...	...	-	-	Error msg	1
3	https://...	pending	-	-	-	-	-	0
```

## Resume behaviour

- rerun reads `batch-state.tsv` and skips completed rows
- lock file (`batch-runner.pid`) prevents double execution
- worker failures do not block remaining offers

## Worker outputs

Each worker should produce:

1. report markdown in `reports/`
2. PDF in `output/`
3. tracker TSV line in `batch/tracker-additions/{id}.tsv`
4. JSON result on stdout

## Error handling


| Error                 | Recovery                                                 |
| --------------------- | -------------------------------------------------------- |
| URL inaccessible      | mark failed and continue                                 |
| JD behind login       | conductor attempts DOM read, otherwise fail and continue |
| Portal layout changed | conductor adapts parsing logic, then continue            |
| Worker crash          | mark failed, continue, retry later                       |
| Conductor crash       | rerun and resume from state file                         |
| PDF failure           | keep report, mark PDF pending                            |



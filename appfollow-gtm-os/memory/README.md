# Institutional memory

Agents write their run artifacts here. This directory is the system's
long-term memory: future runs read it to know what was already reported.

- `followups/` — weekly Actions Check records (Agent 1): findings per
  run, quiet-success notes, fail-stop records. One file per ISO week
  (`{YYYY}-W{WW}.md`).
- `kpi/`       — KPI run records (Agent 2): stub run notes today, weekly
  KPI snapshots once the agent is implemented.

Files here are append-only history: correct mistakes with a follow-up
note, do not rewrite past entries.

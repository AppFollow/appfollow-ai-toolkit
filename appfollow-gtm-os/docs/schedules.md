# Routine schedules

All times below are **Europe/Helsinki**. The trigger platform stores cron
expressions in **UTC**, so expressions are written for Helsinki summer
time (EEST, UTC+3). After the October DST switch (UTC+2), shift each cron
one hour later in UTC to keep the same local time.

Two scheduled routines (CEO 2026-08-11), one per agent, Marketing domain:

| Routine | Agent | Local time | Cron (UTC, summer) | State |
|---------|-------|-----------|--------------------|-------|
| `Marketing Actions Check` | 1 (followup-manager) | Fri 17:00 | `0 14 * * 5` | ⚠️ to create |
| `Marketing KPI Weekly` | 2 (kpi-monitor, STUB) | Mon 09:00 | `0 6 * * 1` | ⚠️ to create |

Each routine fires a **fresh Claude Code session** in this repo's
environment with the matching standalone prompt from `prompts/`
(`marketing-actions-check.md` / `marketing-kpi-weekly.md`). The session
clones this repo, reads `CLAUDE.md`, the config, and the agent
definition, then executes the run.

Required connectors per routine: **Slack, Asana** (Actions Check); the
KPI stub needs none beyond git, its sources are TBD. When creating
routines from an agent session, remember connectors do NOT carry over —
attach them in the claude.ai Routines UI afterwards. A run with a
missing required connector records a failure notice and notifies the
operator — never a silent skip.

> Creating triggers requires a one-time approval in the Claude Code UI.
> Once this repo lives at `AppFollow/appfollow-gtm-os`, open a session
> in it and say "create the GTM triggers per docs/schedules.md", then
> approve the permission dialogs.

## Behavior notes

- **Actions Check (Fri 17:00):** posts only when something is overdue /
  due / newly done / newly gapped — quiet success posts nothing. If the
  current-quarter section (`Q{N} bets`) cannot be resolved, the run
  fail-stops and notifies the operator directly; nothing reaches
  #marketing-team.
- **KPI Weekly (Mon 09:00):** STUB — records a run note only, posts
  nothing, until the `kpi:` block in `config/marketing.yaml` is defined.
  The schedule exists now so the slot is already reserved and the
  plumbing is exercised.

## Managing routines

From any Claude Code session in this environment:

- list: "list my triggers"
- pause everything: "disable all GTM triggers"

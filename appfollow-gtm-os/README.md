# AppFollow AI-Native GTM Operating System

An asynchronous AI system for AppFollow's go-to-market teams. It keeps
quarterly bets moving (owners, due dates, overdue work surfaced weekly)
and will monitor the marketing funnel KPIs continuously — so team
meetings focus on decisions instead of status collection.

This repo contains no application code: it holds the agent definitions,
configuration, and institutional memory. Scheduled routines open a
Claude Code session in this repo and execute one of the agents (see
`CLAUDE.md` for the run contract). It is the GTM sibling of
`appfollow-leadership-os` and mirrors its structure and conventions.

## Design principle: domain-as-config

**Agents are generic; domains are YAML.** An agent definition in
`.claude/agents/` describes HOW to run (scan logic, message format,
fail-stop rules) and reads WHAT to run on from a domain config:

```
.claude/agents/followup-manager.md   ← generic agent (no domain specifics)
config/marketing.yaml                ← Marketing: channel, Asana project, owners, KPIs
prompts/marketing-actions-check.md   ← launch point: agent × domain × schedule
```

Adding **Sales** or **Customer Success** later means adding
`config/sales.yaml` + `prompts/sales-actions-check.md` (and a routine) —
the agent files do not change.

## The agents (Marketing domain, first iteration)

| # | Agent | Cadence | Output |
|---|-------|---------|--------|
| 1 | **Follow-up Manager** (`.claude/agents/followup-manager.md`) | Fri 17:00 Helsinki | **Marketing Actions Check**: overdue tasks & subtasks with permalinks, due this week, done since last run, hygiene gaps by section → posted to #marketing-team (as the CEO). Posts NOTHING when all is on track. |
| 2 | **KPI Monitor** (`.claude/agents/kpi-monitor.md`) | Mon 09:00 Helsinki | **Marketing Weekly** KPI digest → #marketing-team. **STUB — schedule and plumbing exist; metric logic is intentionally not implemented yet.** |

Cron details in `docs/schedules.md`.

## Information architecture

```
Asana ("2025 Marketing" board,   →  system of record: quarterly bets ("Q{N} bets"
      project 1213895660468839)     section), owners, deadlines
Slack                            →  #marketing-team: ALL agent output, posted as the CEO
                                    (operator failure notices go to the CEO directly)
KPI sources (TBD)                →  Agent 2 stub — funnel/visits/signups sources to be
                                    wired in a later iteration
        +
memory/ (this repo)              →  followup matrices, KPI run records — what future
                                    runs learn from
```

## Repo layout

```
CLAUDE.md             the run contract: how a scheduled session executes an agent
config/system.yaml    global config: language, timezone, operator, workspace ids
config/marketing.yaml Marketing domain: Slack channel, Asana project, owners, KPI stub
.claude/agents/       the agent definitions (generic, domain-agnostic)
prompts/              standalone prompts fired by scheduled routines (agent × domain)
knowledge/            repo-canonical team context the agents read
memory/followups/     weekly Actions Check matrices (Agent 1)
memory/kpi/           KPI run records (Agent 2, once implemented)
docs/schedules.md     routine schedule and how to enable/disable each agent
```

## Fail-stop (differs from a quiet skip)

Agent 1 resolves the current quarter from the run date and operates on
the Asana section named `Q{N} bets` (e.g. `Q3 bets`). If no matching
section exists, the run **halts**: nothing is posted to #marketing-team;
the operator (CEO) is notified directly and asked to specify the section
manually. See `CLAUDE.md → Fail-stop protocol`.

## Running an agent manually

Open a Claude Code session in this repo and say, e.g.:

- "Run the marketing actions check"
- "Run the marketing KPI weekly" (will report itself as a stub)

## Adding the next domain (Sales / CS) — checklist

1. Copy `config/marketing.yaml` → `config/sales.yaml`; replace channel,
   Asana project, owner map, KPI block.
2. Copy the two prompts, point them at the new config.
3. Add the routines to `docs/schedules.md` and create them from a session.
4. Do NOT touch `.claude/agents/` — if a domain seems to need an agent
   change, generalize the agent and drive the difference from config.

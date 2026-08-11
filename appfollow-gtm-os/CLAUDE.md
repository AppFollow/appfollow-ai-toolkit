# AppFollow AI-Native GTM Operating System

This repo contains no application code. It is the home of AppFollow's
GTM (go-to-market) agents: their definitions, configuration, and
institutional memory. Scheduled sessions (routines) run in this repo and
execute one of the agents.

It is the GTM sibling of `appfollow-leadership-os` and follows the same
run contract and conventions. **Design principle: agents are generic and
config-driven.** Domain specifics (channels, projects, owners, KPIs) live
in `config/{domain}.yaml` and in the `prompts/*.md` launch points — never
hardcoded in the agent files. Marketing is the first domain; Sales and
Customer Success are added later as new YAML files + new prompts, without
touching the agents.

## How a scheduled run works

1. The trigger prompt names the agent, the mode and the DOMAIN CONFIG
   (e.g. "run the Actions Check for the marketing domain,
   `config/marketing.yaml`").
2. Read `config/system.yaml` first, then the domain config named in the
   prompt — together they define the data source, Slack channel, owners,
   language (English) and timezone (Europe/Helsinki). Team context lives
   in `knowledge/` (repo-canonical for GTM OS — no Guru cards are
   registered for this repo yet; see `config/system.yaml →
   guru_knowledge`).
3. Follow the corresponding agent definition in `.claude/agents/`:
   - `followup-manager.md` — Agent 1, weekly Actions Check (Asana
     follow-up digest per domain)
   - `kpi-monitor.md` — Agent 2, weekly KPI check (STUB — metric logic
     not implemented yet)
4. Persist run artifacts into `memory/` (followups, kpi), commit and push
   **to the repository's default branch**
   (`git push origin HEAD:<default-branch>`), NOT to the session's own
   `claude/...` working branch — stray session branches pile up as
   pending PRs. Never open a pull request for memory commits. If the push
   is rejected (non-fast-forward — another session pushed meanwhile), run
   `git pull --rebase` and push again; only then retry with backoff.
   Committing the run record is mandatory — the Slack post is the primary
   deliverable, but memory is what future runs learn from.

## Hard rules

- Never fabricate metric values or task states. If a source is
  unreadable, escalate instead.
- Slack messages: English, concise, facts + owners + asks. Public
  messages never contain judgments about individuals.
- Slack MCP formatting: messages render STANDARD markdown — bold is
  `**double asterisks**`, italic is `_underscores_`. Single asterisks
  render as italic, NOT bold. Bold the message title and the key items;
  italics only for footnotes.
- Posting identity: routines run with the CEO's Slack MCP connector, so
  every message posts **as the CEO** (`slack.post_as: ceo` in the domain
  config) — same mechanism as Leadership OS. Write accordingly.
- Do not DM team members; post in the domain channel defined in the
  domain config (`slack.channel`). The ONE exception: **operator failure
  notices** (see below) go directly to the operator, never to the team
  channel.
- **Fail-stop protocol** (CEO 2026-08-11): when a run cannot resolve a
  required input — e.g. the current-quarter Asana section
  (`asana.quarter_section_pattern`) does not exist — the run HALTS. Do
  NOT post to the team channel (no warning, no partial digest), do NOT
  silently skip. Notify the operator directly (Slack DM to
  `operator.slack_id` in `config/system.yaml`), state what could not be
  resolved, and ask for a manual section/instruction. Record the failure
  in `memory/`.
- MCP connectors required: Slack, Asana (Agent 1); Agent 2's sources are
  TBD. If a needed connector is unavailable in a scheduled run, record a
  failure notice and notify the operator — do not silently skip the run.

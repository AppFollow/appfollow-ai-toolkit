You are running a scheduled job in the appfollow-gtm-os repository —
AppFollow's AI-Native GTM Operating System.

Task: execute the WEEKLY RUN of Agent 2 (GTM KPI Monitor) for the
MARKETING domain. It is Monday morning.

> STUB NOTICE: Agent 2 is intentionally unfinished (CEO 2026-08-11).
> Until the kpi: block in config/marketing.yaml is defined, this run
> posts NOTHING to #marketing-team and only records a stub run note.

Steps:
1. Read CLAUDE.md, config/system.yaml, config/marketing.yaml.
2. Follow .claude/agents/kpi-monitor.md EXACTLY — while its "Current
   behavior" section says stub, do NOT post to Slack and do NOT compute
   or invent any metrics.
3. Record the stub run note in memory/kpi/{iso-week}.md, commit, push.

<!-- TODO (later iteration): when the kpi: block and sources are
     defined, this prompt switches to: read sources → compute weekly
     KPIs → post "📊 Marketing Weekly — {date range}" to #marketing-team
     (as the CEO) → persist snapshot. Target message shape:
     knowledge/kpi-weekly-intent.md. -->

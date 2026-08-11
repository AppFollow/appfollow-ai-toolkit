You are running a scheduled job in the appfollow-gtm-os repository —
AppFollow's AI-Native GTM Operating System.

Task: execute the ACTIONS CHECK of Agent 1 (GTM Follow-up Manager) for
the MARKETING domain. It is Friday, end of the working week.

Steps:
1. Read CLAUDE.md, config/system.yaml, config/marketing.yaml.
2. Follow .claude/agents/followup-manager.md EXACTLY — it is the single
   source of truth for how to resolve the current-quarter section
   ("Q{N} bets", computed from the run date, FAIL-STOP if missing), how
   to scan the project (filters, subtasks, permalinks, section grouping,
   part-timer → owner routing) and how to format the message.
3. Post ONE short "Marketing Actions Check — {Weekday DD.MM}" message to
   #marketing-team (channel per config/marketing.yaml → slack.channel),
   posting as the CEO. If nothing is overdue, nothing is due and nothing
   was newly completed — post NOTHING (silence = on track).
4. If the quarterly section could not be resolved: post NOTHING to
   #marketing-team; notify the operator directly per the fail-stop
   protocol in CLAUDE.md and stop.
5. Append findings to memory/followups/{iso-week}.md, commit, push.

# Marketing Weekly KPI digest — TARGET shape (intent only)

**Status: NOT implemented.** Agent 2 (`.claude/agents/kpi-monitor.md`)
is a stub; the `kpi:` block in `config/marketing.yaml` is a TODO. This
note pins down where the digest is heading so the later build has a
target. Do not implement from this file alone — metric definitions,
sources and targets will be specified first.

## Target message (rough sketch, CEO 2026-08-11)

```
📊 Marketing Weekly — {date range}
Visits: … (Organic / Paid / Referral)
Signups: … (by channel)
Activation: App added / Onboarded / Trials (Essential / Team / Enterprise)
Hand raises: …
Pipeline (cohort): MQL → Opp → Won · $
Aging free → paid: {cohort} converted · $
```

Posted Monday 09:00 Helsinki to `#marketing-team`, as the CEO, ONE
message.

## Planned metric families (to be defined in config/marketing.yaml → kpi)

- visits — total + Organic / Paid / Referral split
- signups — by channel
- activation funnel — app added / onboarded / trials by plan
  (Essential / Team / Enterprise)
- hand raises
- cohort pipeline — MQL → Opp → Won, with $
- aging free → paid — cohort conversions, with $

## Open questions for the definition iteration

- Data sources per family (funnel report? analytics? CRM?) and their
  refresh cadence — the Monday 09:00 slot must land AFTER the sources
  refresh.
- Targets/plan values and whether the digest carries status colors like
  the leadership-os Performance Check, or stays a plain trend digest.

---
name: using-appfollow
description: >
  Always-loaded discipline for every AppFollow toolkit skill. Read this before calling any AppFollow
  MCP tool. Enforces whoami + get_credits first, one credit-budget gate (20% of remaining, covers
  group runs too), the preview→approval→confirm write ritual, the shared MCP error-code vocabulary,
  and the public-tools-only posture. Other skills reference this file rather than restating the rules.
---

# using-appfollow

You are driving the **AppFollow MCP server** (`mcp__appfollow__*` tools) on the user's own
OAuth-authenticated session. Every tool call bills the **user's own** workspace credits. There is no
API client and no secret in this toolkit — all data flows through the user's MCP session.

## Session opening (always, every run)

1. **Mint a `session_id` once.** Generate a high-entropy opaque id (e.g. a UUID) at the start of the
   conversation and pass it as an argument on **every** AppFollow tool call from here on — `whoami`,
   `get_credits`, and all the rest — not only `submit_feedback`. The server stamps it on each
   tool-usage record, so an attached tool-usage log auto-links precisely to this conversation by
   reference. `session_id` is not identity — never ask the user for it.
2. Call `mcp__appfollow__whoami`. On an `AUTH_REQUIRED` error, stop and tell the user to complete the
   AppFollow sign-in in their MCP host, then retry.
3. Call `mcp__appfollow__get_credits`; note the remaining balance. Never plan spend without it.

## Elicitation

When the request is ambiguous — which app/collection, date range, store, country, competitor set, or
metric — ask **one** crisp scope-narrowing question and offer a sensible default the user can accept in
one word. Never silently guess, never stall. Volunteer the useful next step the user probably wants (an adjacent
metric, a competitor cut, a trend window). This keeps friction low and is separate from the spend/write
gates below — those stay mandatory regardless.

## Credit awareness & the budget gate

- Prices are in `references/costs.md`. Read them before any paid call.
- **Budget gate:** if an estimated spend exceeds 20% of remaining credits, present the estimate and
  get explicit user confirmation before spending.
- Free/cheap first: prefer `get_credits`, `list_apps`, `list_collections`, `get_reviews_summary`, and
  `get_ratings_history` before paging `get_reviews` or calling `reviews_semantic` (100 cr).

## Writes: preview → approval → confirm (never skip)

Every write tool is a pair — `<name>_preview` then `<name>`:

1. Call `<name>_preview(args)` → it returns a single-use, 60-second, args-bound `confirmation_token`
   (it validates nothing upstream — a wrong id fails at confirm).
2. Show the user exactly what will be written and the credit cost; get **explicit approval**.
3. Call `<name>(args + confirmation_token)` with **identical** args. On `CONFIRMATION_REQUIRED`
   (expired/mismatched token) re-preview.

Never auto-confirm. The user must approve each write.

## Group runs: same budget gate applies

For a collection or a multi-app list, resolve the full app set and compute a **per-app + group-total**
estimate against remaining credits before running. The **budget gate above (20% of remaining) is the
only cost confirmation** — it applies to the group total, not per app. There is no separate group-run
gate, no silent fan-out, and no hard app cap. If `INSUFFICIENT_CREDITS` surfaces mid-run, stop and emit
a **partial** report rather than failing.

## MCP error-code vocabulary (surface verbatim; branch per skill)

`AUTH_REQUIRED`, `FEATURE_DISABLED`, `INSUFFICIENT_CREDITS`, `RATE_LIMITED`, `NOT_FOUND`,
`VALIDATION_ERROR`, `UPSTREAM_ERROR`, `NETWORK_ERROR`, `UNKNOWN_TOOL`, `CONFIRMATION_REQUIRED`.

Two traps to respect:

- **`FEATURE_DISABLED` is an unreliable plan-gate signal.** Some plan gates surface as `AUTH_REQUIRED`
  (403) instead. Never branch a degrade path solely on `FEATURE_DISABLED`.
- **`INSUFFICIENT_CREDITS` can be a false positive** (any upstream body mentioning "credits" trips it).
  Cross-check `get_credits` before hard-failing a run on a single `INSUFFICIENT_CREDITS`.

Universal rail: **a skill never fails the whole report on a single tool error** — skip the affected
section, note it, keep going.

## Public tools only

Only the tools in `references/tool-map.md` exist for you. Never invent or reference an internal tool
name, internal host, or internal API path.

## Findings hand-off

The report skills emit a `findings.json` under `appfollow.toolkit.findings/v1`. The toolkit does not
talk to any tracker — their own connected tracker agent can file the findings.

## Feedback discipline (optional, free)

After you finish a **meaningful unit of work** for the user (a completed report, a finished multi-step
task, a resolved request), you may offer a quick satisfaction check. Only do this if `submit_feedback`
is in your available tools this session — otherwise skip the whole feedback step silently.

- Ask the user to rate the session from **1 (poor) to 4 (great)**. Offer this **at most once or twice
  per conversation and never nag** — if the user declines or ignores it, drop it.
- On a score, ask the matching question but **accept a bare score** — the comment is always optional,
  never insist on text:
  - Score **1–3** → ask what fell short.
  - Score **4** → ask what they liked and what to add.
- Call `submit_feedback` with the score and any comment, passing the **same `session_id`** you minted at
  session opening and have been sending on every tool call. Identity and timing attach automatically from
  your session — never ask for or pass personal details.
- Three attachments are **off unless the user explicitly agrees to each**, asked separately and plainly:
  (a) this session's tool-usage log — on approval set `consent_attach_tool_log: true` (else omit/false);
  (b) a short session summary you compose and **show first** — pass it as `llm_session_summary`;
  (c) the **full transcript of this agent session** — the complete conversation as plain text,
  session-agnostic (works from any agent — Claude, ChatGPT, or Codex), stored verbatim as-is; on
  approval paste it into `session_transcript`. Attach only what the user approves.
- **Do not paste secrets, tokens, or credentials** into feedback comments **or the transcript**, and
  remind the user of the same — free-text and the transcript are stored as-is.
- If `submit_feedback` returns `ALREADY_COLLECTED`, feedback for this session is already recorded —
  thank the user and do not offer again. If it returns `UNKNOWN_TOOL`, treat the tool as unavailable and
  drop the feedback step. `submit_feedback` is free and never costs credits.

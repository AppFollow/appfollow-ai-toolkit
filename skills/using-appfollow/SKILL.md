---
name: using-appfollow
description: >-
  Always-loaded discipline for every AppFollow toolkit skill. Read this before calling any AppFollow
  MCP tool. Enforces the preview→approval→confirm write ritual, the shared MCP error-code vocabulary,
  and the public-tools-only posture. Other skills reference this file rather than restating the rules.
---

# using-appfollow

You are driving the **AppFollow MCP server** (the AppFollow tools your session lists) on the user's
own OAuth-authenticated session. Every tool call bills the **user's own** workspace credits. There is
no API client and no secret in this toolkit — all data flows through the user's MCP session.

Tool names here are base names (`whoami`, `get_reviews`, …). They appear with whatever prefix your
host adds (`appfollow`, `plugin_appfollow-ai-toolkit_appfollow`, a claude.ai connector name) — match
the names, not the prefix.

## Session opening (always, every run)

1. **Mint a `session_id` once — only when `submit_feedback` is among the tools your session lists.**
   Generate a high-entropy opaque id (e.g. a UUID) at the start of the conversation and pass it as
   an argument on **every** AppFollow tool call from here on — `whoami`, `get_credits`, and all the
   rest — not only `submit_feedback`. One exception: `get_server_info` takes no arguments at all —
   never pass `session_id` to it. The server stamps it on each tool-usage record, so an attached
   tool-usage log auto-links precisely to this conversation by reference. `session_id` is not
   identity — never ask the user for it. Without `submit_feedback`, mint no `session_id` and pass
   none.
2. Call `whoami`. On an `AUTH_REQUIRED` error, stop and tell the user to complete the AppFollow
   sign-in in their MCP host, then retry.

## Elicitation

When the request is ambiguous — which app/collection, date range, store, country, competitor set, or
metric — ask **one** crisp scope-narrowing question and offer a sensible default the user can accept in
one word. Never silently guess, never stall. Volunteer the useful next step the user probably wants (an adjacent
metric, a competitor cut, a trend window). This keeps friction low and is separate from the write
ritual below — those stay mandatory regardless.

## Writes: preview → approval → confirm (never skip)

Every write tool is a pair — `<name>_preview` then `<name>`:

1. Call `<name>_preview(args)` → it returns a single-use, 60-second, args-bound `confirmation_token`
   (it validates nothing upstream — a wrong id fails at confirm).
2. Show the user exactly what will be written; get **explicit approval**.
3. Call `<name>(args + confirmation_token)` with **identical** args. On `CONFIRMATION_REQUIRED`
   (expired/mismatched token) re-preview.

Never auto-confirm. The user must approve each write.

Not every connection can write. If `whoami.access.write_tools_available` is `false`, the connection is
read-only: do not offer or attempt a write or its `_preview` (nor any name in
`access.unavailable_tools`); an `ACCESS_DENIED` on one is the role, not a wrong id. When the user asks for one, say their
AppFollow role connects read-only and an account owner or admin can make the change. If
`access.workspaces` is `"granted"`, only those workspaces' apps and collections are reachable — take
ids from `list_collections` / `list_apps`.

## Group runs

For a collection or a multi-app list, resolve the full app set before running. No silent fan-out, and
no hard app cap. If `INSUFFICIENT_CREDITS` surfaces mid-run, stop and emit a **partial** report rather
than failing.

## MCP error-code vocabulary (surface verbatim; branch per skill)

`AUTH_REQUIRED`, `ACCESS_DENIED`, `FEATURE_DISABLED`, `INSUFFICIENT_CREDITS`, `RATE_LIMITED`,
`NOT_FOUND`, `VALIDATION_ERROR`, `UPSTREAM_ERROR`, `NETWORK_ERROR`, `UNKNOWN_TOOL`,
`CONFIRMATION_REQUIRED`. `ACCESS_DENIED` means the session is signed in but the call was refused for
this account: most often the collection or app in the call is not the account's — take `apps_id` from
`list_collections` and `ext_id` from `list_apps` — and sometimes a plan feature it lacks (first trap
below).

Two traps to respect:

- **`FEATURE_DISABLED` is an unreliable plan-gate signal.** Some plan gates surface as `ACCESS_DENIED`
  instead — `reviews_semantic` without the Semantic Analysis feature, for one. Never branch a degrade
  path solely on `FEATURE_DISABLED`.
- **`INSUFFICIENT_CREDITS` can be a false positive.** Once one call costs more than the remaining
  balance, the gateway blocks credit-spending calls for the whole account, so a cheaper call that
  follows can be refused with the same code even though the balance covers it. Treat it like any
  other tool error — the universal rail below applies.

Universal rail: **a skill never fails the whole report on a single tool error** — skip the affected
section, note it, keep going.

## Public tools only

Only the AppFollow tools your session lists exist; `references/tool-map.md` documents them. Never
invent or reference an internal tool name, internal host, or internal API path.

## Findings hand-off

The report skills emit a `findings.json` under `appfollow.toolkit.findings/v1`. The toolkit does not
talk to any tracker — their own connected tracker agent can file the findings.

## Feedback discipline (optional, free)

When `submit_feedback` is among the tools your session lists, read `references/feedback.md` after you
finish a meaningful unit of work and before you offer the user a quick rating; otherwise skip
feedback entirely.

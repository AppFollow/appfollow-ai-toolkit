# Feedback discipline (optional, free)

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

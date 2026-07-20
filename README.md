# AppFollow AI Toolkit

**Turn the live AppFollow MCP server into ready-made analyst workflows — for Claude Code and Codex.**

appfollow-ai-toolkit is a native plugin that connects to your AppFollow account over MCP and gives
your agent a set of guided workflows for app-store research and review analysis. No dashboards to
build, no queries to write — just ask, and the agent runs the workflow.

## What you can do with it

Find an app on the App Store or Google Play, dedupe it against what you already track, and — when your plan permits writes — add it to
AppFollow in a couple of turns — no manual search-and-copy-paste.

Ask for a **feedback report** on an app, a whole collection, or just a handful of apps you pick out
of a collection, and get back KPIs with period-over-period deltas, rating and sentiment trends,
anomaly callouts, and a ranked list of the top bugs and feature requests users are actually
mentioning — so you can see what people complain about without reading every review yourself.

Ask for an **ASO report** and get current tracked-keyword positions (plus trends and category-rank
movement where historical data is available), keywords tiered by AppFollow's effectiveness score into worth-it and skip picks, an interactive loop to add the keywords you
choose, and honest metadata suggestions (title/subtitle/keyword-field ideas) you can apply in App
Store Connect or Play Console — so keyword opportunities and ranking risk surface without a manual
spreadsheet pass.

Every report comes out as a self-contained HTML you can open and share, plus a structured findings
file your own tracker-connected agent can file as issues.

## What's inside

- **`using-appfollow`** — the shared discipline every other skill reads first: session setup,
  credit-budget awareness, the preview-approve-confirm ritual for anything that writes data, and a
  common error vocabulary. You won't invoke it directly; the other skills do.
- **`search`** — find an app on the App Store / Google Play and start tracking it in AppFollow.
- **`feedback-analysis`** — the feedback report described above.
- **`aso-analysis`** — the ASO report described above.
- Thin slash commands (`/search`, `/feedback-analysis`, `/aso-analysis`) that just point at the
  matching skill, plus one shared MCP connection (`.mcp.json`) to `https://mcp.appfollow.io/mcp`
  used by both hosts.

This toolkit ships **no bundled API client and no secrets**. Every tool call runs through the AppFollow
MCP server using your own OAuth-authenticated session, billed to your own workspace credits, and only
public MCP tools are ever referenced.

## Quick start

1. **Add the plugin** to your agent host from this repository (Claude Code: add it as a plugin
   marketplace source, then install `appfollow-ai-toolkit` from it; Codex: add it via the same
   repository using its own plugin config). The plugin bundles its skills and the `.mcp.json`
   AppFollow connection, so both hosts pick up the same MCP server automatically.
2. **Authenticate** — the first AppFollow tool call opens your host's normal OAuth sign-in flow. All
   requests after that run under your own AppFollow account.
3. **Ask for a workflow**, either as plain language or a slash command, for example:
   - "Find our app on the App Store and start tracking it" / `/search <app name>`
   - "Build a feedback report for the last 30 days" / `/feedback-analysis <app or collection>`
   - "Run an ASO report and suggest keywords to add" / `/aso-analysis <app or collection>`

## License

[Apache-2.0](./LICENSE) © AppFollow. See [`NOTICE`](./NOTICE) for attribution.

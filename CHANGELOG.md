# Changelog

All notable changes to this project are documented here. Format follows Keep a Changelog; versions
follow SemVer.

## [Unreleased]

## [1.1.0] - 2026-08-03

### Changed

- Reviews guidance aligned with the MCP `get_reviews` contract: the tool is per-app
  (`ext_id` required — `collection_name` is no longer accepted); a collection is gathered by
  looping `list_apps`'s ext_ids, and paging stops when `reviews.page.next` is null.

## [1.0.1] - 2026-08-01

### Fixed

- Codex marketplace descriptor (`.agents/plugins/marketplace.json`): plugin `source` used the
  git/URL form `{"source": "url", "url": "./"}`, which Codex rejects for a plugin living in this
  same repo. Now the local-path form `{"source": "local", "path": "./"}`.

## [1.0.0] - 2026-07-15

First stable release of the **AppFollow AI Toolkit** — a Claude Code + Codex plugin that turns the
public AppFollow MCP server into ready-made analyst workflows. Everything runs on the user's own
OAuth-authenticated MCP session and workspace credits; there is no bundled API client and no secrets,
and only public MCP tools are ever referenced.

What's included:

- **Skills** — `using-appfollow` (always-loaded discipline: session opening, a single
  20%-of-remaining credit gate, the preview → approval → confirm write ritual, the shared error-code
  vocabulary, and the optional feedback flow); `search` (find and start tracking apps);
  `aso-analysis` (keyword report tiered by AppFollow's real effectiveness score, an interactive
  add-keyword loop, and metadata suggestions); `feedback-analysis` (review KPIs with deltas, rating
  and sentiment trends, anomaly callouts, and ranked bugs + feature requests).
- **Output** — each report emits a structured `findings.json` plus a self-contained HTML and Markdown
  report; the user's own connected tracker agent can file the findings.
- **Packaging** — Claude Code and Codex plugin manifests with thin slash commands and a shared
  `.mcp.json` connection to the AppFollow MCP server. The plugin displays as "AppFollow AI Toolkit"
  in both UIs.
- **Licensing** — Apache-2.0.

The skills, the `findings.json` output contract, and the plugin manifests are stable from here: a
breaking change to any of them is a MAJOR version, never a MINOR or PATCH.

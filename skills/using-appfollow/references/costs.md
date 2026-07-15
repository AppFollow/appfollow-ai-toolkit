# Credit costs & the budget rule

Source: `docs.api.appfollow.io/reference/methods-cost-and-availability`.

**Unit convention:** `N cr` = credits per successful request. Qualifiers: `/page` (per 100-row page),
`/review`, `/reply`, `+ N cr / 30 days` (an extra period-scoped charge). `0 cr` means exactly zero —
free. Plan-tier availability (which plan can call a tool at all) is a **separate axis** from cost — it
is never folded into the cost cell; see the per-row notes instead.

| Tool | Cost | Notes |
|---|---|---|
| `whoami` | 0 cr | MCP session check, not billed |
| `get_credits` | 0 cr | MCP session check, not billed |
| `list_apps` | 1 cr | |
| `list_collections` | 1 cr | |
| `get_reviews` | 10 cr / page (100 reviews) | Free plan sees only the last 50 reviews (plan access, not a cost difference) |
| `get_reviews_summary` | 10 cr + 10 cr / 30 days | |
| `get_reviews_ai_summary` | 10 cr | |
| `reviews_semantic` | 100 cr / page (100 reviews) | 3 pages = 300 cr — the most expensive read tool; show as a line item in any estimate |
| `get_ratings_history` | 10 cr + 10 cr / 30 days | |
| `get_gp_console_ratings` | 10 cr + 10 cr / 30 days | |
| `get_rankings` | 10 cr | |
| `list_keywords` | 10 cr | one request = one app/country/device |
| `aso_search_results` | 10 cr | |
| `aso_search_suggestions` | 5 cr | |
| `aso_search_ads_recommendations` | 10 cr | App Store only |
| `aso_featured_apps` | 10 cr + 10 cr / 30 days | |
| `get_aso_report_app_store` | 10 cr | |
| `get_aso_report_gdc_google_play` | 10 cr | |
| `get_app_metadata` | n/a | probed-optional tool; not shipped as an MCP tool (skill probes, falls back to user paste) |
| `add_app` | 1 cr | |
| `add_collection` | 1 cr | |
| `add_keywords` | 5 cr | |
| `reply_to_review` | 10 cr / reply | |
| `report_review_concern` | 10 cr / review | |
| `import_reviews` | 0 cr | unpriced at the gateway; confirm it isn't billed elsewhere |
| `add_app_preview`, `add_collection_preview`, `add_keywords_preview`, `reply_to_review_preview`, `report_review_concern_preview`, `import_reviews_preview` | 0 cr | preview calls mint a confirmation token only, no upstream billed call |
| `submit_feedback` | 0 cr | toolkit-level, no upstream call |

## Budget rule

Ask before spending when an estimate exceeds 20% of remaining credits. Show `reviews_semantic`'s 100
cr/page as a line item in any estimate — never fold it silently into a default.

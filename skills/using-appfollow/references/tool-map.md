# Public MCP tool map

The complete set of AppFollow MCP tools available to this toolkit. Tool names are validated for
parity in CI. Tools surface as `mcp__appfollow__<name>`. Credit costs live in `costs.md`.

## Service

| Tool | Purpose |
|---|---|
| `whoami` | identity / session check (call first) |
| `get_credits` | remaining credit balance (call first) |

## Apps & collections

| Tool | Purpose |
|---|---|
| `list_collections` | list the account's collections |
| `list_apps` | list tracked apps (dedupe target) |
| `add_collection` | create a collection (preview→confirm) |
| `add_collection_preview` | mint a confirmation token for `add_collection` |
| `add_app` | track an app (preview→confirm) |
| `add_app_preview` | mint a confirmation token for `add_app` |

## Reviews

| Tool | Purpose |
|---|---|
| `get_reviews` | paged raw reviews (evidence) |
| `get_reviews_summary` | numeric review aggregate |
| `get_reviews_ai_summary` | AI bullet summary of reviews |
| `reviews_semantic` | per-review tags + sentiment |
| `reply_to_review` | reply to a review (preview→confirm) |
| `reply_to_review_preview` | mint a confirmation token for `reply_to_review` |
| `report_review_concern` | flag a review (preview→confirm) |
| `report_review_concern_preview` | mint a confirmation token for `report_review_concern` |
| `import_reviews` | import reviews (preview→confirm) |
| `import_reviews_preview` | mint a confirmation token for `import_reviews` |

## Ratings

| Tool | Purpose |
|---|---|
| `get_ratings_history` | daily/total ratings series (pass explicit `limit`) |
| `get_gp_console_ratings` | Google Play Console ratings (requires `fields`) |

## ASO

| Tool | Purpose |
|---|---|
| `get_rankings` | category rank |
| `list_keywords` | tracked keyword positions |
| `aso_search_results` | SERP results / own card |
| `aso_search_suggestions` | keyword suggestions |
| `aso_search_ads_recommendations` | Search Ads recommendations (App Store) |
| `aso_featured_apps` | featured-apps placements (requires `ext_id`) |
| `get_aso_report_app_store` | App Store ASO report |
| `get_aso_report_gdc_google_play` | Google Play ASO report |
| `get_app_metadata` | current title/subtitle/description (probe; may be absent — falls back to user paste) |
| `add_keywords` | add tracked keywords (preview→confirm) |
| `add_keywords_preview` | mint a confirmation token for `add_keywords` |

## Feedback

| Tool | Purpose |
|---|---|
| `submit_feedback` | send a 1–4 session-satisfaction score + optional comment(s) back to AppFollow; optional opt-in attachments (tool-usage log, session summary, full session transcript) |

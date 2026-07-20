# feedback-analysis — analysis method

## KPIs & deltas

Compute for the window and the preceding equal period: average rating, rating count, review volume,
reply coverage, and the sentiment split. Report the delta vs the prior window for each.

## Trend series (ratings-derived — complete, never coverage-gated)

From one `get_ratings_history` call spanning both windows (`period=daily`, `type=total`, explicit
`limit` — the default `limit=10` truncates the daily series), compute the daily-average trend and a
7-day rolling average. Split client-side into the two windows.

## Anomaly heuristics (explicit thresholds — print them in the report footer)

- **Rating drop:** 7-day-avg down 0.3 over 14 days, OR a single-day drop of 0.5 with ≥20 ratings.
- **Volume spike:** daily volume greater than `max(10, 3 × trailing-7-day median)`.
- **Negative-share spike:** negative share up 10 percentage points AND above `mean + 2σ`.
- **Review-bombing:** 1-star share above 50% of daily volume for ≥2 consecutive days.
- **Reply-coverage drop:** reply coverage down 15 percentage points.

## Bug / feature mining

Semantic-first when `reviews_semantic` is available and the user opted into its 100 cr; otherwise
**client-side classification of `get_reviews` text is the default fallback** (most plans lack semantic).
Cluster into top bugs and top feature requests; carry 3–5 short quotes (≤200 chars) as evidence.

## Coverage gating (lower-severity flag)

Text-derived anomalies (volume spike, negative-share spike, review-bombing, bug/feature clusters) run on
depth-capped `get_reviews` pages. If `reviews_fetched / reviews_total` is below the floor (default 60%,
always printed), demote those findings to "insufficient coverage — indicative only"; never assert them
as confirmed. Ratings-derived findings are complete and stand on their own.

## Group rollup

Weight per-app metrics by `rating_cnt`; merge cross-app clusters into a single finding with
`affected_apps`.

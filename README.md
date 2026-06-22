[Github Trending Scraper](https://apify.com/knotless_cadence/github-trending-scraper?fpr=data)

# GitHub Trending Scraper — Daily / Weekly / Monthly Trending Repos, Any Language

**Get what's trending on GitHub — repositories sliced by language and time window — plus optional keyword-search results.** Scrapes the public `github.com/trending` page (which has no official REST API) and returns clean structured data for trend tracking, signal detection, and competitive intelligence.

Run daily to build a longitudinal dataset of what's gaining traction. Combine repo-level `periodStars` with longer-term `stars` to identify genuinely breakout projects vs. short-burst hype.

---

## What you actually get (verified against `src/main.js`)

The actor runs **two extraction modes** depending on input — TRENDING (the trending page) and SEARCH (the search results page). Each pushes one record per repo with slightly different fields.

### TRENDING records (12 fields per repo)

```
{
  "owner": "vercel",
  "name": "ai",
  "fullName": "vercel/ai",
  "url": "https://github.com/vercel/ai",
  "description": "The AI Toolkit for TypeScript",
  "language": "TypeScript",
  "stars": 18400,
  "forks": 2370,
  "periodStars": 1840,
  "trendingPeriod": "daily",
  "contributors": ["jaredpalmer", "shuding", "leerob"],
  "scrapedAt": "2026-04-29T12:00:00.000Z"
}
```

`periodStars` = stars gained inside the trending window (daily / weekly / monthly), parsed from the float-right counter on the trending page. `contributors` is the up-to-5 visible avatars on the row (the "Built by" cluster).

### SEARCH records (10 fields per repo)

```
{
  "owner": "openai",
  "name": "openai-cookbook",
  "fullName": "openai/openai-cookbook",
  "url": "https://github.com/openai/openai-cookbook",
  "description": "Examples and guides for using the OpenAI API",
  "language": "Jupyter Notebook",
  "stars": 64200,
  "topics": ["openai", "gpt-4", "examples"],
  "updatedAt": "2026-04-28T09:11:00Z",
  "scrapedAt": "2026-04-29T12:00:00.000Z"
}
```

SEARCH records include `topics[]` (parsed from `.topic-tag`) and `updatedAt` (from the `<relative-time>` element), and walk the `?p=2,3,...` pagination chain via the `rel="next"` link.

**Honest disclosure on what's NOT extracted:** there is no developers-tab scrape, no trending-rank field, no language-color, no license, no sponsorship flag, and no separate `popular_repo` for trending developers. Earlier README versions claimed those — they don't exist in the code.

---

## Input

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `scrapeTrending` | boolean | `true` | If true, fetch the trending page(s). |
| `trendingPeriod` | string | `"daily"` | One of `daily`, `weekly`, `monthly`. Single window per run. |
| `languages` | array | `[]` | Language slugs (`python`, `typescript`, `rust`, `c++`, `jupyter-notebook`...). Empty array = all-languages trending page. |
| `searchQueries` | array | `[]` | Keywords searched against `github.com/search?type=repositories&sort=stars`. SEARCH-mode pagination follows. |
| `maxReposPerSource` | number | `50` | **DEAD PARAMETER** — destructured in main.js but never read. The `.each()` row iterators have no cap. Only effective limit is the crawler-level `maxRequestsPerCrawl: 200` (HTTP requests, not repos). To enforce a real per-source cap, request a custom build. |

---

## How it works

- TRENDING URLs: `https://github.com/trending/{language}?since={trendingPeriod}` (or `https://github.com/trending?since={trendingPeriod}` for all-languages).
- SEARCH URLs: `https://github.com/search?q={query}&type=repositories&sort=stars`.
- CheerioCrawler with `maxConcurrency=5`, `maxRequestsPerCrawl=200`, `requestHandlerTimeoutSecs=30`.
- Default fallback: if `scrapeTrending=false` AND `searchQueries=[]`, the actor pulls daily-all-languages trending so a no-input run still produces data.

---

## Honest limitations (read before bulk runs)

- **`maxReposPerSource` is a DEAD PARAMETER** — destructured at the top of `main.js` but never referenced again. Row iterators (`$('article.Box-row, .Box-row').each(...)` and the SEARCH equivalent) emit ALL parsed rows regardless of this setting. The actual ceiling is `maxRequestsPerCrawl: 200` HTTP requests on the crawler config — NOT a repo count. For a real per-source cap, request a custom build.
- **Search pagination is unbounded** (subject only to the crawler's 200-request ceiling). A broad query can chase rel="next" links into hundreds of pages until the crawler limit kicks in. **Workaround:** use specific queries; if you need explicit per-query caps, request a custom build.
- **TRENDING ≈ 25 rows per page; SEARCH ≈ 10–25 per page.** With 5 languages × ~25 rows = ~125 TRENDING records. Add searches and the global 200-request ceiling caps total HTTP fetches.
- **GitHub HTML changes** — the trending page is stable; SEARCH selectors change more often when GitHub re-skins. The actor uses redundant selectors for both modes; a true rename means a same-week patch.
- **No proxy.** Direct CheerioCrawler fetches from Apify worker IP. GitHub does rate-limit unauthenticated scrapers; with `maxConcurrency=5` against trending pages this is rarely a problem, but high-volume search runs can hit 429. Crawlee's default retry handles transient errors (3 attempts).
- **No GitHub authentication.** This actor scrapes public HTML, NOT the REST/GraphQL API. Auth would require a different code path entirely (custom build).
- **`description` may be empty** for repos without a description set on GitHub.
- **`updatedAt` (SEARCH only) may be `null`** if the `<relative-time>` element isn't surfaced in the row.
- **`periodStars` only on TRENDING records.** SEARCH records do NOT include `periodStars`, `forks`, or `contributors`. Don't write code that assumes both modes share a schema.
- **`contributors` is up to 5 visible avatars** from the "Built by" cluster on the trending row — alt-text only (handles, not commit counts).
- **GitHub Enterprise is not supported** — no `/trending` page.

---

## Python integration

```
from apify_client import ApifyClient
from collections import defaultdict

client = ApifyClient("YOUR_APIFY_TOKEN")

run = client.actor("knotless_cadence/github-trending-scraper").call(
    run_input={
        "scrapeTrending": True,
        "trendingPeriod": "weekly",
        "languages": ["python", "typescript", "rust"],
        "maxReposPerSource": 25,
    }
)
repos = list(client.dataset(run["defaultDatasetId"]).iterate_items())

by_lang = defaultdict(list)
for r in repos:
    by_lang[r.get("language") or "(unknown)"].append(r)

for lang, rs in by_lang.items():
    rs.sort(key=lambda r: r.get("periodStars", 0), reverse=True)
    print(f"\n=== {lang} — top this week ===")
    for r in rs[:5]:
        print(f"  +{r.get('periodStars', 0):>5} stars  {r['fullName']:40s}  {r.get('description','')[:70]}")
```

---

## Trend-tracking pattern (daily cron → S3)

Append daily snapshots; compute breakout-velocity client-side (today's `periodStars` ÷ trailing 7-day median):

```
curl -s -X POST \
  "https://api.apify.com/v2/acts/knotless_cadence~github-trending-scraper/run-sync-get-dataset-items?token=$APIFY_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"scrapeTrending":true,"trendingPeriod":"daily","maxReposPerSource":25}' \
  > "daily_$(date +%Y-%m-%d).json"
```

A repo whose `periodStars` is >2× its 7-day median is a useful breakout signal. Combine with `forks/stars` ratio to filter out star-spam.

---

## Common questions

**Q: Why not use the GitHub REST API directly?**
A: GitHub has no `/trending` API endpoint — the trending page is frontend-only. This actor scrapes it. For other repo data (stars, forks, contributors over time), use the GitHub REST/GraphQL API directly or pair with [GitHub Profile Scraper](https://apify.com/knotless_cadence/github-profile-scraper).

**Q: Can I get stars-over-time for a specific repo?**
A: Not from this actor. Combine with `star-history.com` for historical counts, or build a custom snapshot pipeline (see Custom scraping below).

**Q: How reliable is the scrape?**
A: The trending page HTML has been stable for years. The actor uses redundant selectors (`article.Box-row`, `.Box-row`) and falls back to alternate stars / language extractors. SEARCH selectors are more fragile because GitHub re-skins search more often — when a selector breaks, expect a same-week patch.

**Q: Does this work for GitHub Enterprise?**
A: No — GitHub Enterprise has no public trending page. Actor works against `github.com` only.

**Q: Frequency recommendation?**
A: Daily is the sweet spot — the trending window refreshes every 24h. Running hourly won't give you more signal, only more compute cost.

**Q: Cost per run?**
A: Apify compute-unit pricing. A daily sweep across 5 languages with `maxReposPerSource=25` is typically a few cents.

---

## Export integrations

- CSV / JSON / Excel / HTML (native Apify dataset download)
- Google Sheets (via Apify integration)
- Webhooks on each run
- S3 / GCS direct sync
- Zapier / Make.com / n8n

---

## Related scrapers

| Source | Actor | Data |
| --- | --- | --- |
| GitHub Trending (this) | Trending repos | Open-source signal |
| [GitHub Profile Scraper](https://apify.com/knotless_cadence/github-profile-scraper) | Developer profiles + repos | Recruiting / OSS-strategy |
| [GitHub Issues Scraper](https://apify.com/knotless_cadence/github-issues-scraper) | Issues / PRs | Project-health diagnostics |
| [NPM Package Scraper](https://apify.com/knotless_cadence/npm-package-scraper) | Package metadata | JS ecosystem |
| [Hacker News Scraper](https://apify.com/knotless_cadence/hacker-news-scraper) | Tech discussion | Tech discourse |
| [Public APIs Directory](https://apify.com/knotless_cadence/public-apis-directory) | Free API catalog | API discovery |
| [arXiv Paper Scraper](https://apify.com/knotless_cadence/arxiv-paper-scraper) | Research papers | Academic |

All 31 published actors free to inspect on [Apify Store](https://apify.com/store?search=knotless_cadence).

---

## Custom scraping — pilot tiers

Need a developers-tab scrape, breakout-repo alert pipeline, multi-window aggregator, or competitor-OSS dashboard? Three tiers:

- **Pilot — $97** · 1 actor, basic config, 7-day support. Good entry point — useful for a one-off "top trending in $LANGUAGE this month" report.
- **Standard — $297** · custom actor + Slack/email alerts on results, 30-day support. Most DevRel and competitor-OSS projects fit here.
- **Premium — $797** · custom actor + dashboard + 90-day support + 1 modification round. For ongoing pipelines (daily breakout-detection rollup, language-cohort competitor tracking, multi-source enrichment with Profile + Issues + NPM).

**Email:** [spinov001@gmail.com](mailto:spinov001@gmail.com) — drop the language list and the schema you need; quote within 48h.

**Proof of work:** [31 published Apify scrapers](https://apify.com/knotless_cadence) (78 total in portfolio) — Trustpilot 949 runs, Reddit 80+, Google News 43, Glassdoor 37, Email Extractor 36+. Recently delivered a paid 3-article series for a client in the proxy industry ($150).

**More tips:** [t.me/scraping_ai](https://t.me/scraping_ai) · [blog.spinov.online](https://blog.spinov.online)

---

## Disclaimer

Scrapes the publicly accessible `github.com/trending` and `github.com/search` pages. `maxConcurrency=5` keeps the request rate polite. Not affiliated with GitHub, Inc. or Microsoft Corporation.

*Honest disclosure: TRENDING records have 12 fields, SEARCH records have 10 fields (`periodStars`/`forks`/`contributors` are TRENDING-only; `topics`/`updatedAt` are SEARCH-only). No developers-tab scrape, no `trending_rank`, no `license`, no `primary_color`, no `has_sponsorship`. **`maxReposPerSource` is currently a DEAD PARAMETER** — destructured but never used; only `maxRequestsPerCrawl: 200` HTTP-request ceiling enforces. Search-query pagination is unbounded subject to that ceiling. No proxy, no GitHub auth, scrapes public HTML only.*
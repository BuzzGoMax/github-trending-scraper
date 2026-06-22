[Github Trending Scraper](https://apify.com/harvestlab/github-trending-scraper?fpr=data)

# GitHub Trending Scraper - Star Velocity & AI Digest

> 🧩 **Part of the harvestlab MCP suite** — 36 RAG-ready, AI-agent-payment-ready Apify actors covering ecommerce, social, travel, news, jobs, EU B2B, dev-tools, and government data. [See the full suite →](https://apify.com/harvestlab)

Scrape GitHub's trending repositories **and** trending developers pages, track **cross-run star velocity** (accelerating / fading), and generate a **weekly breakout digest** that aggregates the last N days of snapshots into a VC-analyst narrative: sustained risers, one-hit wonders, dying stars, emerging languages, and breakout-pattern series. Built for developers, venture capitalists, tech scouts, recruiters, and open-source advocates who need structured, actionable data on the latest developer trends.

GitHub Trending is the pulse of the open-source world. Every day, thousands of developers check what's trending to discover new tools, libraries, and frameworks. This actor automates that process, extracting structured data from every trending repository and optionally generating an AI-powered analysis that surfaces the patterns, themes, and opportunities hiding in the raw data.

## What it does

This actor scrapes GitHub's Trending repositories and Trending Developers pages and transforms raw leaderboard snapshots into a continuous momentum-detection feed. Each run extracts repo names, descriptions, languages, total stars, stars gained in the period, fork counts, and contributor lists — structured and clean, ready for dashboards, databases, or downstream pipelines.

The standout capability is **cross-run velocity tracking**. Every run persists star counts to a key-value store keyed by `(language, timeRange)`. Subsequent runs annotate each repo with `velocity_score`, `stars_per_hour`, and a `momentum` label — `accelerating`, `steady`, or `fading` — turning a point-in-time snapshot into an ongoing signal. The same data commercial platforms like Specter and Tracxn charge $10k+/month for is now available at $0.003 per repo.

Schedule it daily and enable **Weekly Breakout Digest** to get an aggregated narrative across the last N days of snapshots: sustained risers (genuinely trending vs. one-day blips), one-hit wonders, dying stars, and emerging-language share shifts. When AI analysis is on, that digest becomes a full **VC-analyst narrative** — headline, executive summary, breakout-winner rationales, language-shift signals, and a 3-5 bullet action-item list for tech scouts.

Filter by 16 programming languages and daily/weekly/monthly time ranges. Flip `scrapeDevelopers` to also pull the Trending Developers leaderboard for recruiter sourcing and developer-relations outreach.

Replaces: daily manual GitHub browsing, expensive developer-trend intelligence subscriptions, and ad-hoc RSS monitoring setups. Goes beyond what free tools like **GitHub Stars**, **Star History**, and the annual **Octoverse** report offer — those give you point-in-time charts or yearly retrospectives, while this actor delivers scheduled cross-run velocity, accelerating/fading flags, and breakout digests on the cadence your workflow actually needs.

## Features

**Complete Repository Data Extraction** — For each trending repository, the actor extracts the full name (owner/repo), repo owner, description, programming language, total star count, stars gained in the selected period, total forks, and a list of contributors who built the project. Every data point is cleaned and normalized for immediate use in spreadsheets, dashboards, and databases.

**Trending Developers Scraping** — Flip `scrapeDevelopers` on to also extract the GitHub Trending Developers leaderboard for the same language and time range. Each developer item includes username, display name, profile URL, avatar, popular repo name, popular repo URL, and popular repo description — perfect for developer-relations outreach, recruiter sourcing, and open-source sponsorship research. Repository and developer items are emitted to the same dataset with distinct `type` fields (`"repository"` vs `"developer"`) so you can filter them downstream.

**Cross-Run Velocity & Momentum Tracking** — On by default. Each run persists a snapshot of star counts to the actor's key-value store, keyed by `(language, timeRange)`. On subsequent runs the actor compares the current stars to the previous snapshot and annotates every repo with:

- `velocity_score` — stars gained since the last run
- `stars_per_hour` — velocity normalized by the time elapsed between snapshots
- `momentum` — one of `"new"` (first appearance), `"accelerating"` (gaining stars >=25% faster than the previous period), `"steady"`, or `"fading"` (<=60% of the prior period)
- `previous_stars_total`, `previous_scraped_at`, `hours_since_last_snapshot` — provenance fields for auditing

Starting with the second run on the same filter combination, the actor also pushes a single **`breakout_summary`** dataset item listing the top-5 rising repos by `stars_per_hour` (excluding fading entries). This converts the actor from a point-in-time snapshot into an ongoing momentum detector — the shape VCs and tech scouts pay $10k+/month for via Specter, Tracxn, and similar platforms. Velocity tracking adds no extra HTTP calls, no extra charge, and is free to disable via `enableVelocityTracking: false` if you only want one-shot snapshots.

**Weekly Breakout Digest** — Flip `generateWeeklyDigest` on to emit a single `weekly_breakout_digest` dataset item per run that aggregates the last N days of snapshot history (default 7 days, configurable up to 60) into a breakout report. Every digest includes:

- `sustained_risers` — top-10 repos present across many snapshots that kept gaining stars across every appearance (what's *really* trending vs. what's a one-day blip)
- `one_hit_wonders` — repos that appeared in exactly one snapshot with a meaningful gain, then vanished (viral moments, launch-day bumps)
- `dying_stars` — repos whose per-snapshot gains are monotonically decreasing (projects losing developer interest, shipped a bad release, or got outcompeted)
- `emerging_languages` — languages whose share of trending repos grew between the oldest and newest snapshot in the window
- `breakout_patterns` — raw `(snapshot_at, stars_total, stars_gained)` series for the top-10 risers, suitable for sparkline or chart rendering downstream

When `enableAiAnalysis` is also on, the digest includes a **VC-analyst narrative** generated from the aggregates — headline, executive summary, sustained-risers analysis, breakout winners list with "why it matters" rationales, one-hit-wonder read, fading-stars analysis, language-shift signal, investment thesis, and a 3-5 bullet action-item list for tech scouts. Charged as `weekly-digest-generated` at $0.10 per digest. Requires at least 2 prior snapshots on the same `(language, timeRange)` filter within the window — schedule daily runs for a week before enabling.

**Flexible Language and Time Range Filters** — Filter trending repositories by 16 popular programming languages including Python, JavaScript, TypeScript, Rust, Go, Java, C++, C#, Ruby, Swift, Kotlin, PHP, R, Scala, and Shell. Choose between daily, weekly, or monthly trending periods to match your analysis needs.

**Spoken Language Support** — Optionally filter by the spoken language of the repository README. Use standard language codes like "en" for English or "zh" for Chinese to find trending projects accessible in your preferred language.

**AI-Powered Trend Analysis** — Enable the built-in AI analysis to generate a comprehensive report that identifies trending categories, emerging technologies, investment-worthy projects, developer recommendations, and market themes. The AI examines all scraped repositories together to find patterns that are impossible to spot manually.

**Multi-LLM Provider Support** — Choose between OpenRouter (recommended — 300+ models), Anthropic (Claude), Google AI (Gemini), OpenAI (GPT), or Ollama (self-hosted) for AI analysis. Specify any model available on your chosen platform or use the smart defaults.

**Pay-Per-Event Pricing** — Only pay for what you use. Each scraped repository costs $0.003, each developer $0.003, each AI analysis report $0.05, and each weekly digest $0.10. No monthly fees, no minimum commitments.

**Server-Rendered HTML Parsing** — GitHub's trending page is fully server-rendered, so this actor uses fast HTTP requests with BeautifulSoup instead of a headless browser. This means faster execution times, lower compute costs, and higher reliability compared to browser-based scrapers.

**Proxy Support** — Use Apify's built-in proxy infrastructure or configure your own proxies for reliable access to GitHub trending pages from any location.

## Use Cases

**Developers and Engineering Teams** — Stay on top of the tools and libraries gaining traction in your language ecosystem. Discover new frameworks before they go mainstream. Use weekly runs to keep a pulse on what the open-source community is building and adopting.

**Venture Capitalists and Investors** — Identify projects with explosive star velocity that signal strong developer interest and potential commercial opportunity. The AI analysis highlights investment-worthy projects and market themes to inform your deal flow. The weekly digest VC-analyst narrative surfaces investment theses you can act on immediately.

**Tech Scouts and Analysts** — Generate structured trend reports for technology landscape analysis. Track how programming languages, frameworks, and tools rise and fall in popularity over time by running the actor on a schedule across different filters.

**Recruiters and Talent Acquisition** — Find the developers behind trending projects. The built-by data includes contributor usernames and profile links, helping you identify top talent actively building popular open-source software. Enable `scrapeDevelopers` to get the full GitHub Trending Developers leaderboard.

**CTOs and Technical Decision Makers** — Evaluate which technologies are gaining community momentum before committing your team to a technology stack. Use the AI analysis to understand broader ecosystem dynamics and make informed build-vs-buy decisions.

**Open-Source Advocates and Community Managers** — Monitor trending projects in your domain to identify collaboration opportunities, competing projects, and emerging community needs. Use scheduled runs to build a historical record of open-source trends.

**News and Research Publishers** — Generate weekly "State of Open Source" digests automatically. The VC-analyst narrative output is ready to paste into newsletters, analyst briefings, or internal knowledge bases with minimal editing.

## Input

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| language | string | any | Programming language filter. Options: any, python, javascript, typescript, rust, go, java, c++, c#, ruby, swift, kotlin, php, r, scala, shell |
| timeRange | string | daily | Trending time range. Options: daily, weekly, monthly |
| maxRepos | integer | 25 | Maximum repos to scrape (1-100) |
| spokenLanguage | string | null | Spoken language code for README filter (e.g. "en", "zh") |
| scrapeDevelopers | boolean | false | Also scrape the GitHub Trending Developers page. Adds items with `type="developer"` to the dataset. |
| enableVelocityTracking | boolean | true | Persist cross-run snapshots in the KV store; emit `velocity_score`, `stars_per_hour`, `momentum` on each repo and a `breakout_summary` item per run. Free — no extra HTTP calls, no extra charge. Set `false` for pure one-shot snapshots. |
| generateWeeklyDigest | boolean | false | Emit a `weekly_breakout_digest` item aggregating the last N days of snapshot history. Charged at $0.10 per digest. Requires >=2 prior snapshots on the same filter in the window. |
| digestWindowDays | integer | 7 | Rolling window size (days) for the weekly digest. Range 1-60. Ignored unless `generateWeeklyDigest=true`. |
| enableAiAnalysis | boolean | false | Generate AI trend analysis report. Set to `true` to enable. Requires an API key. |
| llmProvider | string | openrouter | LLM provider: "openrouter", "anthropic", "google", "openai", or "ollama" |
| llmModel | string | null | Specific model name. Leave empty to use the provider default. |
| openrouterApiKey | string | null | OpenRouter API key (required if using OpenRouter). Get one free at openrouter.ai/keys. |
| anthropicApiKey | string | null | Anthropic API key (required if using Anthropic). Get one at console.anthropic.com. |
| googleApiKey | string | null | Google AI API key (required if using Google). Get one at aistudio.google.com/app/apikey. |
| openaiApiKey | string | null | OpenAI API key (required if using OpenAI). Get one at platform.openai.com/api-keys. |
| ollamaBaseUrl | string | [http://localhost:11434](http://localhost:11434) | Base URL for self-hosted Ollama instance. |
| proxyConfiguration | object | null | Proxy settings for accessing GitHub. Recommended for high-volume runs. |

**CLI aliases:** `programmingLanguage` / `lang` for `language`; `since` for `timeRange`; `maxItems` for `maxRepos`; `spokenLanguageCode` for `spokenLanguage`; `includeDevelopers` for `scrapeDevelopers`.

**Pricing:**

| Event | Price | Description |
| --- | --- | --- |
| repo-scraped | $0.003 | Charged per trending repository extracted |
| developer-scraped | $0.003 | Charged per trending developer extracted |
| ai-analysis-completed | $0.05 | Charged per AI trend analysis report |
| weekly-digest-generated | $0.10 | Charged per weekly breakout digest item |

**Example cost calculations:**

- 25 repos (daily trending, default): 25 x $0.003 = **$0.075**
- 25 repos + AI analysis: $0.075 + $0.05 = **$0.125**
- 25 repos + 25 developers + AI analysis: $0.15 + $0.05 = **$0.20**
- 100 repos + AI analysis: 100 x $0.003 + $0.05 = **$0.35**
- Daily scheduled run (25 repos + AI, 30 days): 30 x $0.125 = **$3.75/month**
- Weekly digest (25 repos + AI + digest, weekly): $0.125 + $0.10 = **$0.225/week ≈ $0.98/month**

## Output

Each trending repository produces a dataset item with this structure:

```
{
    "type": "repository",
    "rank": 1,
    "repo_name": "exampleuser/awesome-project",
    "owner": "exampleuser",
    "author": "exampleuser",
    "repo_url": "https://github.com/exampleuser/awesome-project",
    "url": "https://github.com/exampleuser/awesome-project",
    "description": "A blazing fast tool for building modern web applications",
    "language": "TypeScript",
    "programming_language": "TypeScript",
    "stars_total": 15234,
    "stars_period_count": 342,
    "stars_gained": 342,
    "stars_period_label": "342 stars today",
    "forks_total": 1205,
    "built_by": [
        {
            "username": "contributor1",
            "avatar_url": "https://avatars.githubusercontent.com/u/12345?s=40&v=4",
            "profile_url": "https://github.com/contributor1"
        }
    ],
    "velocity_score": 180,
    "stars_per_hour": 7.5,
    "momentum": "accelerating",
    "previous_stars_total": 15054,
    "previous_scraped_at": "2026-04-10T12:00:00Z",
    "hours_since_last_snapshot": 24.0,
    "scraped_at": "2026-04-11T12:00:00Z"
}
```

On the first run for a given `(language, timeRange)` combination the velocity fields are `null` / `momentum="new"` — momentum data fills in from the second run onward. When the previous snapshot is available, a single `breakout_summary` item is also emitted per run:

```
{
    "type": "breakout_summary",
    "language_filter": "python",
    "time_range": "daily",
    "snapshot_at": "2026-04-11T12:00:00Z",
    "previous_snapshot_at": "2026-04-10T12:00:00Z",
    "breakout_count": 5,
    "top_breakouts": [
        {
            "repo_name": "exampleuser/awesome-project",
            "url": "https://github.com/exampleuser/awesome-project",
            "stars_total": 15234,
            "stars_gained": 342,
            "velocity_score": 180,
            "stars_per_hour": 7.5,
            "momentum": "accelerating",
            "language": "TypeScript",
            "description": "A blazing fast tool for building modern web applications"
        }
    ]
}
```

When `scrapeDevelopers=true`, developer items are also emitted:

```
{
    "type": "developer",
    "rank": 1,
    "username": "octocat",
    "name": "The Octocat",
    "profile_url": "https://github.com/octocat",
    "url": "https://github.com/octocat",
    "avatar_url": "https://avatars.githubusercontent.com/u/583231?s=96&v=4",
    "popular_repo": "octocat/Hello-World",
    "popular_repo_url": "https://github.com/octocat/Hello-World",
    "popular_repo_description": "My first repository on GitHub!",
    "time_range": "daily",
    "period_label": "today",
    "scraped_at": "2026-04-22T12:00:00Z"
}
```

When `generateWeeklyDigest=true` and the rolling history has at least 2 snapshots in the window, a `weekly_breakout_digest` item is emitted (narrative is populated only when `enableAiAnalysis` is also on):

```
{
    "type": "weekly_breakout_digest",
    "language_filter": "python",
    "time_range": "daily",
    "window_days": 7,
    "snapshots_in_window": 7,
    "oldest_snapshot_at": "2026-04-15T12:00:00+00:00",
    "newest_snapshot_at": "2026-04-22T12:00:00+00:00",
    "sustained_risers": [
        {
            "repo_name": "exampleuser/awesome-project",
            "appearances": 7,
            "total_stars_gained": 2100,
            "first_stars_total": 15054,
            "last_stars_total": 17154,
            "stars_delta": 2100,
            "first_seen_at": "2026-04-15T12:00:00+00:00",
            "last_seen_at": "2026-04-22T12:00:00+00:00"
        }
    ],
    "one_hit_wonders": [{"repo_name": "flash/pan", "seen_at": "2026-04-17T12:00:00+00:00", "stars_total": 842, "stars_gained": 420}],
    "dying_stars": [{"repo_name": "old/project", "appearances": 4, "peak_stars_gained": 210, "final_stars_gained": 15, "drop_pct": 92.9}],
    "emerging_languages": [{"language": "Rust", "oldest_share_pct": 16.0, "newest_share_pct": 32.0, "share_delta_pct": 16.0}],
    "breakout_patterns": [{"repo_name": "exampleuser/awesome-project", "total_stars_gained": 2100, "appearances": 7, "series": [{"snapshot_at": "2026-04-15T12:00:00+00:00", "stars_total": 15054, "stars_gained": 280}]}],
    "narrative": {
        "headline": "Rust's share of trending doubled this week, led by infra-layer tooling",
        "executive_summary": "...",
        "sustained_risers_narrative": "...",
        "breakout_winners": ["..."],
        "investment_thesis": "...",
        "action_items": ["..."]
    },
    "generated_at": "2026-04-22T12:01:00+00:00"
}
```

When AI analysis is enabled, an additional dataset item is produced:

```
{
    "type": "trend_analysis",
    "repos_analyzed": 25,
    "language_filter": "any",
    "time_range": "daily",
    "trending_categories": ["AI/ML Tools", "Developer Productivity", "Web Frameworks"],
    "emerging_technologies": ["..."],
    "most_active_languages": ["Python", "TypeScript", "Rust"],
    "investment_worthy_projects": ["..."],
    "ecosystem_insights": "...",
    "developer_recommendations": ["..."],
    "market_themes": ["..."],
    "technology_patterns": ["..."],
    "opportunity_signals": ["..."],
    "generated_at": "2026-04-11T12:01:00Z"
}
```

**Performance:** Since GitHub trending is server-rendered HTML, the actor completes in under 10 seconds for scraping alone. AI analysis adds 10-30 seconds depending on the LLM provider and model chosen. Total execution time is typically under 40 seconds. Platform costs (Apify compute units) are minimal because the actor uses plain HTTP requests instead of a headless browser.

**Note:** The GitHub trending page typically shows a maximum of 25 repositories per language/time-range combination. Setting `maxRepos` above 25 will only return more results if GitHub displays more than 25 on the page. Trending data is a snapshot in time — schedule regular runs to build your own historical dataset.

## Quick Start

The simplest way to run this actor with default settings (daily trending, any language, 25 repos):

```
{
    "language": "any",
    "timeRange": "daily",
    "maxRepos": 25,
    "enableAiAnalysis": false
}
```

To scrape Python trending repos this week with AI analysis via OpenRouter:

```
{
    "language": "python",
    "timeRange": "weekly",
    "maxRepos": 50,
    "enableAiAnalysis": true,
    "llmProvider": "openrouter",
    "openrouterApiKey": "YOUR_OPENROUTER_API_KEY"
}
```

For the full weekly-digest setup (scheduled daily, emits a VC-analyst weekly digest every run once a week of history has accumulated):

```
{
    "language": "python",
    "timeRange": "daily",
    "maxRepos": 25,
    "enableVelocityTracking": true,
    "generateWeeklyDigest": true,
    "digestWindowDays": 7,
    "enableAiAnalysis": true,
    "llmProvider": "openrouter",
    "openrouterApiKey": "YOUR_OPENROUTER_API_KEY"
}
```

**Scheduling tip:** Use Apify's built-in scheduler to run this actor daily at the same UTC hour. Keep the `language` and `timeRange` parameters identical across all scheduled runs — this is what allows the velocity tracker and weekly digest to accumulate meaningful history. Each unique `(language, timeRange)` combination is tracked in its own independent snapshot slot.

**Getting started with AI analysis:** OpenRouter is the cheapest option (free tier available). Create an account at [openrouter.ai](https://openrouter.ai/keys), copy your API key, and paste it into the `openrouterApiKey` field. The default model (`google/gemini-2.0-flash-001`) produces high-quality analysis at minimal cost.

**Getting started with the weekly digest:** Enable `generateWeeklyDigest: true` and schedule daily runs. The digest will not appear on the first run (no history yet). After the second daily run you will see a `weekly_breakout_digest` item in the dataset. By the seventh daily run you have a full "this week" view including all five aggregated signals (sustained risers, one-hit wonders, dying stars, emerging languages, and breakout pattern series).

## Use with AI agents

github-trending-scraper's output is agent-ready — every run returns ranked trending repos with cross-run star velocity (`accelerating` / `steady` / `fading` momentum flags), a `breakout_summary`, and an optional VC-analyst AI digest as structured JSON. Wrap this actor as a tool inside a VC tech-radar agent, AI-tool-watch workflow, or dev-rel sourcing pipeline — a Premium-tier replacement for the annual Octoverse retrospective with the cadence and structured signal Specter / Tracxn charge $10k+/month for.

**LangChain — `track_github_trending` tool (Octoverse-displacement angle):**

```
from langchain.tools import Tool
from apify_client import ApifyClient

client = ApifyClient("YOUR_APIFY_TOKEN")

def track_github_trending(params: dict) -> list:
    run = client.actor("harvestlab/github-trending-scraper").call(run_input={
        "language": params.get("language", "any"),
        "timeRange": params.get("timeRange", "weekly"),
        "enableVelocityTracking": params.get("trackVelocity", True),
        "enableAiAnalysis": True,
    })
    return list(client.dataset(run["defaultDatasetId"]).iterate_items())

track_trending_tool = Tool(
    name="track_github_trending",
    description="Scrape GitHub Trending repos with cross-run star velocity (accelerating/fading flags) + AI trend analysis. Replaces Octoverse-style yearly retrospectives with on-demand structured signal.",
    func=track_github_trending,
)
# agent.invoke({"input": "What Python infra tools are accelerating this week?"})
```

**LangGraph — node in a VC tech-radar graph:**

```
from langgraph.graph import StateGraph
from apify_client import ApifyClient

client = ApifyClient("YOUR_APIFY_TOKEN")

def github_trending_node(state: dict) -> dict:
    run = client.actor("harvestlab/github-trending-scraper").call(run_input={
        "language": state["language"],
        "timeRange": "weekly",
        "enableVelocityTracking": True,
        "generateWeeklyDigest": True,
        "enableAiAnalysis": True,
    })
    items = list(client.dataset(run["defaultDatasetId"]).iterate_items())
    repos = [i for i in items if i.get("type") == "repository"]
    accelerating = [r for r in repos if r.get("momentum") == "accelerating"]
    digest = next((i for i in items if i.get("type") == "weekly_breakout_digest"), None)
    return {**state, "ranked_repos": repos, "accelerating": accelerating, "digest": digest}

graph = StateGraph(dict)
graph.add_node("github_trending", github_trending_node)
# wire into downstream tech-radar / AI-tool-watch / deal-flow / Slack-digest nodes
```

See Apify's [`actor-templates/js-langchain`](https://github.com/apify/actor-templates/tree/master/templates/js-langchain) and [`js-langgraph-agent`](https://github.com/apify/actor-templates/tree/master/templates/js-langgraph-agent) for full reference setups.

**Related actors for cross-referencing:**

- [News Monitor](https://apify.com/harvestlab/news-monitor) — track which GitHub trending repos are also generating media coverage
- [Reddit Scraper](https://apify.com/harvestlab/reddit-scraper) — cross-reference with developer community sentiment on programming subreddits
- [Product Hunt Scraper](https://apify.com/harvestlab/producthunt-scraper) — identify repos that also launched on Product Hunt
- [Contact Extractor](https://apify.com/harvestlab/contact-extractor) — extract contact details from trending repo maintainers for dev-relations outreach

## Troubleshooting

### Actor returns 0 repos despite a valid language filter

GitHub Trending can return an empty page when GitHub is under maintenance or temporarily rate-limiting anonymous requests. Wait 15 minutes and retry. If the issue recurs across multiple runs, GitHub may have changed the trending page HTML structure — check the actor changelog for an updated version. Using Apify proxy (`proxyConfiguration.useApifyProxy: true`) reduces the chance of being rate-limited since requests rotate across different IPs.

### Language filter returns results for the wrong language or no results

Language names are case-insensitive but must match GitHub's internal slug exactly. Common mistakes: `"c++"` works but `"cpp"` does not; `"c#"` works but `"csharp"` does not; `"javascript"` works but `"js"` does not. Check the parameter table in the Input section for the full accepted list. Unrecognized language strings cause GitHub to return the full all-language trending page rather than an error.

### `timeRange` values other than daily/weekly/monthly produce no results

The `timeRange` parameter accepts exactly three values: `"daily"`, `"weekly"`, and `"monthly"`. Values like `"today"`, `"this-week"`, `"week"`, or `"d"` are not recognized and cause the actor to default to `"daily"`. Verify your input matches one of the three accepted strings exactly.

### Velocity and momentum fields are null on the second run

Cross-run velocity requires the exact same `(language, timeRange)` combination to be used in both runs. If you change `language` from `"python"` to `"any"` or `timeRange` from `"daily"` to `"weekly"` between runs, a new snapshot slot is created and no prior baseline exists. Keep these parameters identical across scheduled runs. The first run for any filter combination always emits `momentum: "new"` and null velocity fields — data appears from the second run onward.

### Weekly digest item not appearing in the output dataset

The `weekly_breakout_digest` item requires three conditions: (1) `generateWeeklyDigest: true`, (2) at least 2 prior snapshots exist in the rolling window for the same `(language, timeRange)` filter, and (3) the snapshots fall within `digestWindowDays` of each other. On the first run there is no history — the digest only appears from the second run onward. Schedule the actor to run daily for at least two days before expecting a digest.

### AI analysis fails or is skipped silently

If `enableAiAnalysis: true` but no analysis item appears in the dataset, the API key is missing or invalid. Provide it via the input field (e.g., `openrouterApiKey`) or the corresponding environment variable (`OPENROUTER_API_KEY`, `ANTHROPIC_API_KEY`, `GOOGLE_API_KEY`, or `OPENAI_API_KEY`). The actor logs a warning and continues scraping repos without the AI step — it does not abort the run. Set `enableAiAnalysis: false` to suppress the warning.

### How does the weekly breakout digest work?

Every scrape appends a snapshot to a rolling-history slot (`velocity-history-{language}-{timeRange}`) in the named key-value store. When `generateWeeklyDigest=true`, the actor loads all snapshots within the last `digestWindowDays` days, runs five aggregators (sustained risers, one-hit wonders, dying stars, emerging languages, breakout patterns), and emits a single `weekly_breakout_digest` dataset item per run. With `enableAiAnalysis=true`, the digest is handed to your chosen LLM provider with a VC-analyst prompt that returns a structured narrative. To get meaningful digests, schedule the actor to run daily on the same filter for at least a week — the digest needs at least 2 snapshots in the window (and ideally 7 for the "this week" view). Up to 60 snapshots are retained per filter slot.

### How often does GitHub update the trending page?

GitHub updates the trending page roughly every hour, though the exact frequency is not publicly documented. Running this actor more than once per hour for the same filter is unlikely to yield new data.

### Can I scrape trending for languages not in the dropdown?

The preset list covers 16 popular languages. GitHub supports many more — you can enter any language name that GitHub recognizes in the language field, and the actor will attempt to use it in the URL.

## Pricing

This actor uses **pay-per-event** pricing — you only pay for what you scrape.

| Event | Price | When it fires |
| --- | --- | --- |
| `repo-scraped` | $0.003 | Each trending repository record extracted |
| `developer-scraped` | $0.003 | Each trending developer extracted |
| `ai-analysis-completed` | $0.05 | Per run when AI trend analysis report is generated |
| `weekly-digest-generated` | $0.10 | Per rolling N-day breakout digest item emitted |

**vs. commercial alternatives**: Eoris and Star-History.com offer chart views but no programmatic API or bulk export; Augur (Linux Foundation) and OSSF Scorecard focus on security, not trend velocity. This actor uses pay-per-event — $0.002/repository with no subscription, delivering structured JSON ready for RAG or dashboards.

---

## Scheduling and webhooks

Schedule daily GitHub Trending runs in Apify Console to track the developer ecosystem in real time. Wire a webhookUrl in n8n or Make to push today's trending repos into a Slack #dev-radar channel, Notion research database, or newsletter content queue the moment a run finishes. Typical pipeline: language=python daily trending -> n8n -> Slack digest of top 10 AI/ML repos every morning.

---

## Legal and Compliance

GitHub's trending page ([https://github.com/trending](https://github.com/trending)) is publicly accessible to anyone with a web browser. No authentication or login is required to view it. The data displayed — repository names, descriptions, star counts, and contributor information — is intentionally made public by the repository owners who chose to host their projects on GitHub as public repositories.

This actor accesses only the publicly available trending page. It does not access any private repositories, user accounts, or authenticated API endpoints. It does not bypass any access controls or rate limits.

Users of this actor should review GitHub's Terms of Service ([https://docs.github.com/en/site-policy/github-terms/github-terms-of-service](https://docs.github.com/en/site-policy/github-terms/github-terms-of-service)) and ensure their use case complies. Automated access to GitHub is subject to their acceptable use policies. Very high-frequency scraping may trigger GitHub's rate limiting — using Apify proxies is recommended for production use, and the actor enforces polite crawling with exponential backoff on rate-limit responses.

This actor is provided as-is for legitimate research, analysis, and monitoring purposes. Users are responsible for ensuring their use of the scraped data complies with all applicable laws and terms of service, including data protection regulations such as GDPR. While GitHub trending data consists primarily of public repository metadata rather than personal data, users should assess their own compliance obligations under applicable data protection laws. Developer username and avatar data (included in contributor lists and the trending developers feed) may constitute personal data under certain jurisdictions — users are responsible for handling this data appropriately.
[Github Trending Scraper](https://apify.com/klondikeking/github-trending-scraper?fpr=data)

# GitHub Trending Repositories Scraper — Extract Popular Open Source Projects

Scrape GitHub Trending to discover the most popular open source repositories by programming language, time period, and spoken language. This Actor extracts repository metadata including stars, forks, descriptions, and trending rankings in structured JSON format for analysis, monitoring, and lead generation.

GitHub Trending is the go-to destination for developers tracking what's hot in the open source community. Whether you're researching emerging technologies, monitoring competitor projects, or curating content for a developer newsletter, this scraper provides clean, structured data without the hassle of manual extraction.

## Use Cases

- **Technology Research** — Track which programming languages and frameworks are gaining traction among developers
- **Competitor Monitoring** — Keep tabs on trending projects in your niche to understand market movements
- **Developer Newsletter Curation** — Source content for weekly digests featuring the best new open source projects
- **Recruiting Intelligence** — Identify rapidly growing projects to find companies hiring or talented developers to recruit
- **Investment Research** — Monitor GitHub activity as an indicator of technology trends and startup momentum
- **Portfolio Tracking** — Watch your own projects climb the trending charts and measure their community impact

## Input

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `timeRange` | String | Yes | Trending period: `daily`, `weekly`, or `monthly` (default: daily) |
| `language` | String | No | Filter by programming language (e.g., `python`, `javascript`, `typescript`, `go`, `rust`). Leave empty for all languages. |
| `spokenLanguage` | String | No | Filter by spoken language code (e.g., `en`, `zh`, `es`, `de`). Leave empty for all. |
| `maxItems` | Number | No | Maximum repositories to extract, 1-100 (default: 25) |
| `proxyConfiguration` | Object | No | Proxy settings for requests |

## Output

The Actor outputs a dataset with the following fields:

```
{
  "rank": 1,
  "repository": "owner/repo-name",
  "owner": "owner",
  "name": "repo-name",
  "url": "https://github.com/owner/repo-name",
  "description": "Project description",
  "language": "TypeScript",
  "stars": 15000,
  "starsToday": 245,
  "forks": 1200,
  "timeRange": "daily",
  "scrapedAt": "2026-04-23T08:30:00.000Z"
}
```

## Pricing

Pay per event: $0.001 per repository extracted (minimum charge: $0.001 per run).

## Limitations

- GitHub Trending displays a maximum of 25 repositories per language/time combination
- Repository descriptions may be truncated for projects with very long README intros
- Stars "today" reflects the selected time range (daily/weekly/monthly), not calendar days
- Rate limiting may apply for very high-frequency scraping

## FAQ

**Q: Can I scrape all programming languages at once?**

A: Yes, leave the `language` field empty to get trending repositories across all languages.

**Q: Does this work with private repositories?**

A: No, this scraper only extracts data from public repositories shown on GitHub Trending.

**Q: How often is GitHub Trending updated?**

A: GitHub updates trending rankings throughout the day based on recent star activity.

**Q: Can I schedule this Actor to run daily?**

A: Yes, use Apify Schedules to automate daily, weekly, or custom interval runs.

## Changelog

- **v1.0.0** — Initial release with full GitHub Trending extraction support

---

*Built for developers who need clean data without the overhead of browser automation. Fast, reliable, and cost-effective.*
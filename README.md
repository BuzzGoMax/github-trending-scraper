[Github Trending Scraper](https://apify.com/openclawmara/github-trending-scraper?fpr=data)

# GitHub Trending Scraper

Scrape trending repositories and developers from [GitHub Trending](https://github.com/trending). Get real-time data on what's hot in the developer community — filterable by language, time range, and spoken language.

## What can it do?

- **Trending repositories** — Top repos by stars gained in a time period
- **Trending developers** — Most active developers with their popular repos
- **Filter by language** — Python, JavaScript, Rust, Go, or any of 500+ languages
- **Time ranges** — Daily, weekly, or monthly trends
- **Spoken language** — Filter by content language (English, Chinese, etc.)

## Why use this scraper?

- ⚡ **Fast** — Direct HTML parsing, no API token needed
- 📊 **Rich data** — Stars, forks, language, description, stars gained today
- 👨‍💻 **Developer profiles** — Username, popular repo, avatar
- 🔄 **Always current** — GitHub Trending updates in real-time
- 🌍 **Multi-language** — Filter by programming and spoken language

## Input examples

### Get all trending repos today

```
{
  "mode": "repositories",
  "since": "daily"
}
```

### Trending Python repos this week

```
{
  "mode": "repositories",
  "language": "python",
  "since": "weekly"
}
```

### Trending developers this month

```
{
  "mode": "developers",
  "since": "monthly"
}
```

### Trending Rust repos

```
{
  "mode": "repositories",
  "language": "rust",
  "since": "daily"
}
```

## Output example (repository)

```
{
  "rank": 1,
  "username": "openai",
  "reponame": "codex",
  "url": "https://github.com/openai/codex",
  "description": "Lightweight coding agent that runs in your terminal",
  "language": "TypeScript",
  "stars": 15200,
  "forks": 1100,
  "starsToday": 2300,
  "builtBy": ["developer1", "developer2"]
}
```

## Use cases

- **Open source intelligence** — Track new popular projects early
- **Technology radar** — Monitor which languages/frameworks are gaining traction
- **Hiring research** — Find active developers in specific technologies
- **Investment signals** — Spot trending developer tools and infrastructure
- **Content creation** — Write about what's trending in tech

## Pricing

Free to use. You only pay for Apify platform usage (compute and storage).

## Limitations

- GitHub Trending shows ~25 repos per page
- Trending data is based on recent star activity (not total stars)
- HTML scraping — may need updates if GitHub changes their page layout
[Github Trending Scraper](https://apify.com/optimus-fulcria/github-trending-scraper?fpr=data)

Scrape GitHub Trending for the hottest repositories and developers. Filter by programming language, time period, and spoken language.

## Features

- **Trending Repositories** - Daily, weekly, or monthly top repos
- **Trending Developers** - Top contributors with popular repos
- **Language Filtering** - Filter by any programming language (Python, JavaScript, Rust, Go, etc.)
- **Multi-language** - Scrape multiple languages in a single run
- **Rich Data** - Stars, forks, descriptions, contributors, language, and growth metrics

## Output Data

### Repositories

- Repository full name (owner/name), URL
- Description and programming language
- Total stars and forks
- Stars gained in the period (today/this week/this month)
- Top contributors (built by)
- Trending rank

### Developers

- Username, display name, avatar URL
- Most popular repository with description
- Trending rank

## Use Cases

- Track trending open-source projects
- Discover new tools and libraries in your tech stack
- Monitor competitor or industry repositories
- Build datasets of popular projects for research
- Find trending developers for recruiting
- Automated daily/weekly reports on GitHub trends

## Input Examples

### Today's trending repos (all languages)

```
{
    "scrapeRepos": true,
    "since": "daily"
}
```

### Python and JavaScript weekly trends with developers

```
{
    "scrapeRepos": true,
    "scrapeDevelopers": true,
    "languages": ["python", "javascript"],
    "since": "weekly"
}
```

### Monthly top Rust projects

```
{
    "scrapeRepos": true,
    "languages": ["rust"],
    "since": "monthly"
}
```
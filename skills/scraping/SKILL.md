---
name: scraping
description: >
  Scrape football/soccer statistics from websites like WhoScored, FBref, and Transfermarkt.
  Use this skill whenever the user wants to collect, download, extract, or refresh player stats, team stats, match results, league tables, or any other football data from the web.
  Also trigger when the user mentions "scrape", "crawl", "fetch data", "pull stats", "update data", or references any football data source URL. Covers browser automation, HTML parsing, anti-bot handling, rate-limiting, and data export to CSV/JSON/Parquet.
---

# Football Scraping Skill

## Overview

This skill handles all web scraping tasks for football statistics. It uses **Playwright** for browser automation (required because target sites render data via JavaScript) and **BeautifulSoup** for HTML parsing once pages are loaded.

## When to Use

- The user asks to scrape, fetch, or download football data from a website.
- The user provides a URL to a football stats page and wants data extracted.
- The user wants to refresh or update the local `data/raw/` directory with new data.
- The user mentions WhoScored, FBref, Transfermarkt, Understat, or similar sites.

## Key Principles

1. **Respect rate limits.** Always wait between requests (minimum 3–5 seconds). Never hammer a server.
2. **Use headless browsers.** Most football stats sites require JavaScript rendering.
3. **Handle pagination.** Many stat tables span multiple pages — always check for "next page" controls.
4. **Fail gracefully.** If a page structure changes or a selector breaks, log the error clearly and continue with other targets.
5. **Store raw data faithfully.** Save the raw extracted data as-is to `data/raw/` before any cleaning.

## Workflow

```
1. Load target config        →  config/settings.yaml
2. Launch headless browser    →  src/scraping/browser.py
3. Navigate & wait for data   →  handle dynamic JS rendering
4. Extract HTML tables/data   →  src/scraping/parsers.py
5. Save raw output            →  data/raw/<source>_<date>.csv
6. Log results                →  src/utils/logging.py
```

## Target Sites & Strategies

### WhoScored (whoscored.com)
- **Rendering:** Heavy JS — requires Playwright with full page load waits.
- **Data location:** Statistics tables rendered client-side; wait for `div.stat-table` or equivalent selectors.
- **Pagination:** Dropdowns for leagues, seasons; "next" buttons for player lists.
- **Anti-bot:** Cloudflare protection. Use realistic user-agents, add random delays, consider stealth plugins.

### FBref (fbref.com)
- **Rendering:** Mostly server-rendered HTML — lighter browser needs.
- **Data location:** Standard `<table>` elements with clear IDs.
- **Rate limit:** Be conservative; FBref is community-friendly but will block aggressive scrapers.

### Transfermarkt (transfermarkt.com)
- **Rendering:** Mixed. Some JS, some server-rendered.
- **Data location:** Tables inside `div.responsive-table`.
- **Note:** Market values and transfer data live here.

## Configuration Reference

All scraping targets are defined in `config/settings.yaml`. Each target entry includes:

| Field            | Description                                      |
|------------------|--------------------------------------------------|
| `name`           | Identifier for this scraping target              |
| `url`            | Base URL to start scraping                       |
| `selectors`      | CSS selectors for the data tables                |
| `pagination`     | How to navigate pages (button click, URL param)  |
| `rate_limit`     | Seconds to wait between requests                 |
| `output_format`  | `csv`, `json`, or `parquet`                      |

## Error Handling

- **Selector not found:** Log a warning, skip the page, continue pipeline.
- **Timeout:** Retry up to 3 times with exponential backoff.
- **Blocked (403/captcha):** Stop the pipeline for that source, alert the user.
- **Empty data:** Log and flag — could indicate a site redesign.

## Dependencies

- `playwright` — browser automation
- `beautifulsoup4` — HTML parsing
- `lxml` — fast HTML/XML parser backend
- `pandas` — tabular data handling during extraction
- `pyyaml` — config loading

## Files

| File                        | Purpose                              |
|-----------------------------|--------------------------------------|
| `src/scraping/browser.py`   | Browser launch, navigation, waits    |
| `src/scraping/parsers.py`   | HTML → structured data extraction    |
| `src/scraping/pipelines.py` | Orchestrates full scraping runs      |
| `config/settings.yaml`      | Target definitions and selectors     |

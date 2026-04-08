# Football Analytics

A Python-based project for scraping and analyzing football (soccer) statistics from public websites such as [WhoScored](https://www.whoscored.com/statistics).

## Project Goals

1. **Data Scraping** — Collect player, team, and match statistics from football data websites using browser automation and structured extraction.
2. **Data Analysis** — Clean, transform, and analyze the scraped data to produce insights such as player rankings, team comparisons, performance trends, and advanced metrics.

## Project Structure

```
football-analytics/
├── skills/                  # Claude skill definitions
│   ├── scraping/            # Web scraping skill
│   │   └── SKILL.md
│   └── analysis/            # Data analysis skill
│       └── SKILL.md
├── subagents/               # Subagent instructions
│   ├── mister/              # Orchestrator (Opus 4.6)
│   │   └── SUBAGENT.md
│   ├── volante/             # Scraper (Haiku 4.5)
│   │   └── SUBAGENT.md
│   └── meia/                # Analyzer (Sonnet 4.6)
│       └── SUBAGENT.md
├── src/                     # Source code
│   ├── scraping/            # Scraping modules
│   │   ├── __init__.py
│   │   ├── browser.py       # Browser automation (Playwright/Selenium)
│   │   ├── parsers.py       # HTML parsing & data extraction
│   │   └── pipelines.py     # Scraping orchestration pipelines
│   ├── analysis/            # Analysis modules
│   │   ├── __init__.py
│   │   ├── cleaning.py      # Data cleaning & normalization
│   │   ├── metrics.py       # Derived stats & advanced metrics
│   │   └── visualizations.py# Charts and visual outputs
│   └── utils/               # Shared utilities
│       ├── __init__.py
│       ├── io.py            # File I/O helpers (CSV, JSON, Parquet)
│       └── logging.py       # Logging configuration
├── data/
│   ├── raw/                 # Raw scraped data (gitignored)
│   └── processed/           # Cleaned & transformed data (gitignored)
├── tests/                   # Unit and integration tests
│   └── __init__.py
├── config/
│   └── settings.yaml        # Scraping targets, selectors, rate limits
├── docs/                    # Extended documentation
│   ├── SCRAPING_GUIDE.md
│   └── ANALYSIS_GUIDE.md
├── .gitignore
├── requirements.txt
├── pyproject.toml
└── README.md
```

## Quick Start

```bash
# Clone the repo
git clone https://github.com/<your-username>/football-analytics.git
cd football-analytics

# Create a virtual environment
python -m venv .venv
source .venv/bin/activate   # Linux/macOS
# .venv\Scripts\activate    # Windows

# Install dependencies
pip install -r requirements.txt
playwright install chromium  # if using Playwright for scraping
```

## Usage

### Scraping

```bash
# Run the default scraping pipeline
python -m src.scraping.pipelines --config config/settings.yaml
```

### Analysis

```bash
# Run analysis on the latest scraped data
python -m src.analysis.metrics --input data/processed/
```

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/my-feature`)
3. Commit your changes (`git commit -m 'Add my feature'`)
4. Push to the branch (`git push origin feature/my-feature`)
5. Open a Pull Request

## License

MIT
---
name: analysis
description: >
  Analyze football/soccer statistics to produce insights, rankings, comparisons, and visualizations. Use this skill whenever the user wants to clean raw scraped data, compute advanced metrics (xG, progressive passes, pressing stats), compare players or teams, detect trends across seasons, build rankings, or generate charts and reports. Also trigger when the user mentions "analyze", "compare", "rank", "trend", "visualize",
  "xG", "per 90", "percentile", or any football analytics concept. Covers data cleaning, statistical computation, and visualization with matplotlib/seaborn/plotly.
---

# Football Analysis Skill

## Overview

This skill handles all data analysis tasks on football statistics. It takes raw scraped data from `data/raw/`, cleans and normalizes it, computes derived metrics, and produces insights through tables and visualizations stored in `data/processed/`.

## When to Use

- The user wants to clean, filter, or normalize raw football data.
- The user asks to compute advanced metrics (e.g., per-90 stats, percentiles, xG-based metrics).
- The user wants to compare players, teams, or leagues.
- The user asks for charts, rankings, trend lines, or any visual output.
- The user wants a report or summary of the data.

## Key Principles

1. **Clean before analyzing.** Always run data through `cleaning.py` before metrics or visualization.
2. **Normalize for fairness.** Use per-90-minute stats when comparing players with different minutes played.
3. **Document assumptions.** Every derived metric should have a docstring explaining how it's calculated.
4. **Reproducibility.** All analysis should be runnable from a single command with clear inputs and outputs.
5. **Visual clarity.** Charts should have titles, axis labels, legends, and sensible color palettes.

## Workflow

```
1. Load raw data               →  data/raw/*.csv
2. Clean & normalize           →  src/analysis/cleaning.py
3. Compute metrics             →  src/analysis/metrics.py
4. Generate visualizations     →  src/analysis/visualizations.py
5. Export results              →  data/processed/
```

## Data Cleaning Steps

Performed by `src/analysis/cleaning.py`:

- **Column normalization** — Standardize column names to snake_case.
- **Type casting** — Convert numeric columns from strings (e.g., "1,234" → 1234).
- **Missing data** — Flag and handle NaN values (drop, fill, or interpolate depending on context).
- **Deduplication** — Remove duplicate rows from overlapping scraping runs.
- **Filtering** — Apply minimum appearance thresholds (e.g., ≥ 900 minutes for per-90 stats).

## Metric Categories

### Basic Per-90 Normalization
Convert raw totals into per-90-minute rates:
`metric_per90 = (raw_metric / minutes_played) * 90`

### Percentile Rankings
Rank players within their position group using percentiles (0–100).

### Composite Scores
Weighted combinations of metrics to create custom indices, e.g.:
- **Attacking Threat** = weighted sum of goals, assists, key passes, xG per 90
- **Defensive Solidity** = weighted sum of tackles, interceptions, blocks, aerial duels won

### Trend Analysis
Compare a player's or team's metrics across multiple seasons or match-weeks to detect improvement, decline, or consistency.

## Visualization Types

| Chart Type       | Use Case                                  | Library     |
|------------------|-------------------------------------------|-------------|
| Bar chart        | Top-N player rankings                     | matplotlib  |
| Radar chart      | Multi-metric player profiles              | matplotlib  |
| Scatter plot     | xG vs actual goals, metric correlations   | seaborn     |
| Line chart       | Season-over-season trends                 | plotly      |
| Heatmap          | Correlation matrices, positional data     | seaborn     |
| Box plot         | Distribution comparisons across leagues   | matplotlib  |

## Configuration

Analysis parameters are set via `config/settings.yaml` under the `analysis` section:

| Field                   | Description                                      |
|-------------------------|--------------------------------------------------|
| `min_minutes`           | Minimum minutes played to include a player       |
| `per90_metrics`         | List of columns to normalize per 90              |
| `composite_weights`     | Weights for composite score calculations         |
| `output_format`         | `csv`, `json`, `parquet` for processed data      |
| `chart_style`           | matplotlib style sheet to use                    |

## Dependencies

- `pandas` — data manipulation
- `numpy` — numerical operations
- `scipy` — statistical functions (percentiles, z-scores)
- `matplotlib` — static charts
- `seaborn` — statistical visualizations
- `plotly` — interactive charts

## Files

| File                             | Purpose                                     |
|----------------------------------|---------------------------------------------|
| `src/analysis/cleaning.py`       | Raw data → clean, normalized DataFrames     |
| `src/analysis/metrics.py`        | Compute per-90, percentiles, composites     |
| `src/analysis/visualizations.py` | Generate charts and visual outputs          |
| `src/utils/io.py`               | Read/write CSV, JSON, Parquet               |

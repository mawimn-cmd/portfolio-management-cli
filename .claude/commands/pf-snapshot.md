# /pf-snapshot — Historical Portfolio Snapshot

You are a portfolio data historian for governance trend tracking. Capture the current portfolio state as a point-in-time snapshot.

## Input
$ARGUMENTS

## Data Files
- Config: `data/config.json`
- Projects: `data/projects.csv`
- Financials: `data/financials.csv`
- Budget: `data/budget.csv`
- Pipeline: `data/pipeline.csv`
- Snapshots: `data/snapshots.csv`

## Behavior

### This is a READ-ONLY + APPEND command
Read current state, calculate metrics, append one row to snapshots.csv.

### Step 1: Calculate current metrics
Read all data files and compute:

```
date = today's date (YYYY-MM-DD)
total_projects = count of rows in projects.csv (excluding Cancelled)
total_budget = sum of planned_budget from budget.csv (all projects)
total_spend = sum of actual_spend from budget.csv (all projects)
portfolio_npv = sum of npv from financials.csv (first row per project, where npv is not blank)
avg_cpi = average of cpi from budget.csv (where cpi is not blank)
avg_spi = average of spi from budget.csv (where spi is not blank)
active_count = count of projects with status "Active"
pipeline_count = count of rows in pipeline.csv
notes = auto-generated brief note (e.g., "Monthly snapshot" or "Post-planning cycle")
```

If $ARGUMENTS contains a note, use it as the notes field.

### Step 2: Compare to last snapshot
Read the last row in snapshots.csv (if any). Calculate deltas:

```
PORTFOLIO SNAPSHOT — [Date]

Current State:
  Total Projects:  [n]
  Total Budget:    $[x]
  Total Spend:     $[y]  ([z]% of budget)
  Portfolio NPV:   $[x]
  Avg CPI:         [x]
  Avg SPI:         [x]
  Active Projects: [n]
  Pipeline Items:  [n]
```

If previous snapshot exists:
```
DELTA FROM LAST SNAPSHOT ([previous date])
  Metric           | Previous    | Current     | Change
  Total Projects   | [x]         | [y]         | [+/-z]
  Total Budget     | $[x]        | $[y]        | [+/-$z]
  Total Spend      | $[x]        | $[y]        | [+/-$z]
  Portfolio NPV    | $[x]        | $[y]        | [+/-$z] ([+/-n]%)
  Avg CPI          | [x]         | [y]         | [+/-z]
  Avg SPI          | [x]         | [y]         | [+/-z]
  Active Projects  | [x]         | [y]         | [+/-z]
  Pipeline Items   | [x]         | [y]         | [+/-z]

Highlights:
  [Auto-generated: largest positive change]
  [Auto-generated: largest concern]
```

### Step 3: Show trend (if 3+ snapshots)
If there are 3 or more snapshots, show a mini trend:
```
PORTFOLIO TREND (last [n] snapshots)
Date        | Projects | NPV    | CPI  | SPI  | Spend
2026-01-15  | 4        | $450K  | 1.02 | 0.98 | $200K
2026-02-15  | 5        | $520K  | 0.95 | 0.92 | $380K
2026-03-15  | 5        | $490K  | 0.88 | 0.85 | $550K  ← current
                                  ↓      ↓
                         CPI declining  SPI declining
```

### Step 4: Append to snapshots.csv
Write one new row with all calculated values.

### Step 5: Refresh Dashboard
Auto-refresh `reports/dashboard.html` with current portfolio data:
1. Read all data files from `data/` (config.json, strategy.json, projects.csv, financials.csv, teams.csv, capacity.csv, budget.csv, pipeline.csv, snapshots.csv)
2. Read `reports/dashboard.html`
3. Parse each CSV into an array of objects (column headers as keys)
4. Build a JSON object: `{"generated_at":"[today YYYY-MM-DD]","config":{...},"strategy":{...},"projects":[...],"financials":[...],"teams":[...],"capacity":[...],"budget":[...],"pipeline":[...],"snapshots":[...]}`
5. Replace the content of the `<script id="embedded-data" type="application/json">` tag with this JSON
6. Write the updated HTML back to `reports/dashboard.html`
7. Tell the user: "Dashboard refreshed at `reports/dashboard.html`."

### PMO Insights
```
PMO INSIGHTS
─────────────────────────────────────────────────────────────────
Trend Assessment:
  • [Interpret the trend — e.g., "CPI has declined for 3 consecutive snapshots,
    indicating a systemic cost efficiency issue across the portfolio."]
  • [Flag any metrics that breached config.pmo_settings thresholds since last snapshot]

Governance Implications:
  • [If CPI/SPI declining]: "Declining delivery performance warrants discussion
    at the next governance review."
  • [If portfolio NPV changed significantly]: "Portfolio NPV [increased/decreased]
    by [x]% — driven by [primary factor]."

Snapshot saved: [date]
[n] total snapshots on record

Next snapshot recommended: [date + reporting_cadence period]
Or run /pf-snapshot anytime to capture current state.
```

## Rules
- One snapshot per invocation (append, never overwrite)
- Date format: YYYY-MM-DD
- If metrics can't be calculated (no data), store as blank (not 0)
- Portfolio NPV = sum of individual project NPVs
- Avg CPI/SPI: only average projects that have these metrics
- Notes field: use $ARGUMENTS if provided, else auto-generate based on context
- Trend analysis requires 3+ snapshots
- Flag concerning trends (3+ consecutive declines in CPI or SPI)
- This command should be fast — minimal interaction, mostly automated
- Use config.pmo_settings.reporting_cadence for next snapshot recommendation

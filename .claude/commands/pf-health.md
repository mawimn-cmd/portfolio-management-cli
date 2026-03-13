# /pf-health — Portfolio Health Dashboard

You are a portfolio health analyst for governance leadership. Generate a comprehensive dashboard showing portfolio-wide KPIs and delivery health.

## Input
$ARGUMENTS

## Data Files
- Config: `data/config.json`
- Projects: `data/projects.csv`
- Financials: `data/financials.csv`
- Teams: `data/teams.csv`
- Capacity: `data/capacity.csv`
- Budget: `data/budget.csv`
- Pipeline: `data/pipeline.csv`
- Snapshots: `data/snapshots.csv`

## Behavior

### This is a READ-ONLY command
No questions asked (unless data is completely empty). Read all data files and generate dashboard.

### Step 1: Load all data
Read every data file. If all files are empty (no data rows), say:
"No portfolio data found. Start with `/bp-setup` to configure your portfolio, then `/bp-project` to register projects."

### Step 2: Generate dashboard

```
╔══════════════════════════════════════════════════════════════════════╗
║              PORTFOLIO HEALTH DASHBOARD                             ║
║              [Portfolio Name] — [Today's Date]                      ║
╚══════════════════════════════════════════════════════════════════════╝

OVERVIEW
  Total Projects:  [n]  (Active: [a] | Approved: [b] | Pipeline: [c] | On-Hold: [d])
  Total Teams:     [n]  (Headcount: [total people])
  Pipeline Items:  [n]

───────────────────────────────────────────────────────────────────────
FINANCIAL HEALTH
  Total Portfolio Budget:    $[sum of all planned budgets]
  Total Spend to Date:       $[sum of all actual_spend]
  Budget Utilization:        [spend/budget × 100]%
  Portfolio NPV:             $[sum of project NPVs from financials.csv]
  Total Revenue (projected): $[sum across all projects/years]
  Total Investment:          $[sum of all costs]
  Portfolio ROI:             [total revenue - total cost / total cost × 100]%

───────────────────────────────────────────────────────────────────────
PROJECT STATUS
  ID   | Name              | Status  | Phase     | CPI  | SPI  | Flag
  P001 | AI Platform       | Active  | Execution | 0.88 | 0.85 | Red
  P002 | CRM Enhancement   | Active  | Planning  | 1.02 | 0.98 | Green
  P003 | Data Migration    | Approved| Ideation  | —    | —    | —
  ...

  Green — On Track: [n] projects
  Yellow — At Risk:  [n] projects
  Red — Needs Attention: [n] projects
  — Not tracking: [n] projects

───────────────────────────────────────────────────────────────────────
STRATEGIC ALLOCATION
  Bucket              | # Projects | Budget    | Target | Actual | Status
  Core Growth         | [n]        | $[x]      | 60%    | [y]%   | [over/under/on target]
  Adjacent            | [n]        | $[x]      | 20%    | [y]%   | [over/under/on target]
  Transformational    | [n]        | $[x]      | 20%    | [y]%   | [over/under/on target]

───────────────────────────────────────────────────────────────────────
RESOURCE ALLOCATION
  Team            | Project   | Headcount | Utilization | Status
  Backend Eng     | P001      | 8         | 95%         | Red
  Data Platform   | P002      | 5         | 78%         | Green
  ...

  Avg Utilization: [x]%
  Over-allocated:  [n] teams (>95%)
  Available:       [n] FTEs across all teams

───────────────────────────────────────────────────────────────────────
DELIVERY PERFORMANCE (projects with EVM data)
  Avg CPI: [x]  (Portfolio cost efficiency)
  Avg SPI: [x]  (Portfolio schedule performance)

  [If avg CPI < 0.95]: Portfolio trending over budget
  [If avg SPI < 0.95]: Portfolio trending behind schedule

───────────────────────────────────────────────────────────────────────
GOVERNANCE ALERTS
  [List any projects/teams with Red status]
  [Flag over-allocated teams]
  [Flag projects with no assigned team]
  [Flag projects past target_end_date]
  [Flag strategic bucket imbalances]
  [Flag projects breaching config.pmo_settings escalation thresholds]

───────────────────────────────────────────────────────────────────────
PIPELINE PREVIEW
  [n] items in pipeline | Total estimated value: $[sum]
  Nearest start: [item] targeting [date]
  Run /pf-pipeline for details

───────────────────────────────────────────────────────────────────────
DELTA FROM LAST SNAPSHOT
  [If snapshots.csv has data, show comparison]
  Metric              | Last ([date]) | Now        | Change
  Total Projects      | [x]           | [y]        | +/-[z]
  Portfolio NPV       | $[x]          | $[y]       | +/-$[z]
  Avg CPI             | [x]           | [y]        | +/-[z]
  Avg SPI             | [x]           | [y]        | +/-[z]
  Active Projects     | [x]           | [y]        | +/-[z]

═══════════════════════════════════════════════════════════════════════
Quick actions:
  /pf-review    — Run formal governance review
  /pf-scenario  — What-if analysis
  /pf-snapshot  — Save this state for trend tracking
  /pf-report    — Generate executive report
```

### Step 3: PMO Insights
After the dashboard, provide:

```
PMO INSIGHTS
─────────────────────────────────────────────────────────────────
Portfolio Health Assessment:
  • [What's going well — e.g., "Portfolio NPV is positive and [x] of [y] projects
    are on track. Strategic allocation is within [x]% of targets."]
  • [What needs attention — e.g., "[Project] has CPI and SPI below escalation
    thresholds — requires immediate governance attention."]
  • [Trend commentary if snapshot data available]

Decisions Needed:
  • [Key decisions surfaced by the data — e.g., "Re-baseline P001 or accept
    the projected overrun?"]

Recommended Actions:
  1. [Highest-priority action with suggested owner]
  2. [Second-priority action]
  3. [Strategic recommendation]

Escalation:
  • [If any project breaches config.pmo_settings thresholds]: "Projects requiring
    escalation per governance policy: [list with CPI/SPI values]"
```

### Step 4: Refresh Dashboard
Auto-refresh `reports/dashboard.html` with current portfolio data:
1. Read all data files from `data/` (config.json, strategy.json, projects.csv, financials.csv, teams.csv, capacity.csv, budget.csv, pipeline.csv, snapshots.csv)
2. Read `reports/dashboard.html`
3. Parse each CSV into an array of objects (column headers as keys)
4. Build a JSON object: `{"generated_at":"[today YYYY-MM-DD]","config":{...},"strategy":{...},"projects":[...],"financials":[...],"teams":[...],"capacity":[...],"budget":[...],"pipeline":[...],"snapshots":[...]}`
5. Replace the content of the `<script id="embedded-data" type="application/json">` tag with this JSON
6. Write the updated HTML back to `reports/dashboard.html`
7. Tell the user: "Dashboard refreshed at `reports/dashboard.html`."

## Rules
- This is purely read-only — never modify any data files
- If a data section has no data (e.g., no EVM data), show "No data" for that section, don't skip it
- CPI/SPI status: Green ≥ 0.95, Yellow 0.85-0.95, Red < 0.85
- Capacity status: Green < 85%, Yellow 85-95%, Red > 95%
- Calculate strategic allocation % from budget data, compare to targets
- Available FTEs = headcount of teams with utilization < 85%
- Portfolio NPV = sum of individual project NPVs (from first row per project in financials.csv)
- Portfolio ROI uses total projected revenue vs total projected costs from financials.csv
- Delta section only shown if at least 1 snapshot exists
- Gracefully handle missing data — show what you have, indicate what's missing
- Use config.pmo_settings for escalation threshold comparisons
- Use governance language: "portfolio health", "delivery performance", "governance alerts"

# /pf-one-pager — Generate Project One-Pagers

You are a portfolio documentation assistant for governance leadership. Generate standardized one-page summaries from existing portfolio data.

## Input
$ARGUMENTS

## Data Files
- Config: `data/config.json`
- Projects: `data/projects.csv`
- Financials: `data/financials.csv`
- Teams: `data/teams.csv`
- Capacity: `data/capacity.csv`
- Budget: `data/budget.csv`

## Behavior

### Step 1: Determine scope
If $ARGUMENTS contains:
- A project ID or name → generate one-pager for that project
- **"all"** → generate one-pagers for all active projects
- Nothing → ask "Which project? (or 'all' for all active projects)"

### Step 2: Read project data
For each project, read all available data from CSVs:
- Project details from projects.csv
- Financial projections from financials.csv
- Team info from teams.csv
- Capacity data from capacity.csv
- Budget data from budget.csv

### Step 3: Generate one-pager
Create a markdown document for each project.

**Filename**: `reports/one-pager-[project_id]-[project_name_slug].md`

**Template**:
```markdown
# [Project Name] — One-Pager
**ID**: [id] | **Status**: [status] | **Phase**: [phase] | **Date**: [today]

---

## Overview
| Field | Value |
|-------|-------|
| Owner | [owner] |
| Org Unit | [org_unit] |
| Type | [type] |
| Strategic Bucket | [bucket] |
| Priority Rank | [rank or "Unranked"] |
| Timeline | [start] → [end] |

## Description
[description from projects.csv]

## Strategic Fit
- **Bucket**: [strategic_bucket]
- **Priority Rank**: [rank] of [total projects]

## Financial Summary
| Metric | Value |
|--------|-------|
| Total Investment | $[sum of all costs from financials.csv] |
| Total Revenue (projected) | $[sum of all revenue] |
| NPV | $[npv from financials.csv] |
| ROI | [roi]% |
| Payback Period | [payback] years |
| Breakeven | FY[year] |

### P&L Snapshot (first and last year)
| | FY[first] | FY[last] |
|---|-----------|----------|
| Revenue | $[x] | $[y] |
| Costs | $[x] | $[y] |
| Operating Income | $[x] | $[y] |

## Delivery Team & Capacity
| Team | Mode | Headcount | Monthly Cost | Utilization |
|------|------|-----------|-------------|-------------|
| [team] | [mode] | [n] | $[cost] | [util]% |

**Total Team Cost**: $[sum]/month ($[annual]/year)

## Budget & Delivery Health
| Metric | Value |
|--------|-------|
| Total Budget | $[planned] |
| Spent to Date | $[actual] |
| Remaining | $[remaining] |
| CPI | [cpi] |
| SPI | [spi] |
| Status | [Green/Yellow/Red] |

## Key Risks
[If risk data available from business case, list top 3]
[If not: "Risk assessment pending — run /bp-business-case"]

## Governance Notes
[If investment > approval_threshold]: "Investment exceeds governance threshold — board approval required"
[If CPI/SPI breach escalation thresholds]: "Delivery health below escalation threshold — governance attention required"

---
*Auto-generated from portfolio data on [date]*
```

### Step 4: Handle missing data gracefully
For any section where data doesn't exist:
- Financial Summary: "Financial model pending — run `/bp-financials [id]`"
- Team: "No delivery team assigned — run `/bp-team`"
- Budget: "Budget not yet established — run `/bp-budget [id]`"
- Don't skip sections — show what's missing so the governance reader knows what to fill

### Step 5: Output

**Single project**: Display the one-pager inline AND save to reports/

**All projects**: Save each to reports/ and show a summary:
```
Generated [n] one-pagers:
  reports/one-pager-P001-ai-platform.md
  reports/one-pager-P002-crm-enhancement.md
  reports/one-pager-P003-data-migration.md

Summary:
  ID   | Name            | Investment | NPV    | Status | Data Complete?
  P001 | AI Platform     | $1.5M      | $112K  | Red    | Yes
  P002 | CRM Enhancement | $800K      | $350K  | Green  | Yes
  P003 | Data Migration  | $500K      | N/A    | —      | Partial (no financials)
```

### PMO Insights (after generation)
```
PMO INSIGHTS
─────────────────────────────────────────────────────────────────
  • [If any projects have incomplete data]: "[n] projects have incomplete data —
    address before the next governance review."
  • [Comment on portfolio balance visible from one-pagers]
  • [Flag any projects that may not survive prioritization based on their numbers]
```

## Rules
- One-pagers are auto-generated from existing data — no user questions needed
- Create reports/ directory if it doesn't exist
- Handle missing data gracefully — show "pending" not errors
- Slug project name: lowercase, replace spaces/special chars with hyphens
- For "all" mode, only include projects with status: Pipeline, Approved, Active
- Financial summary uses the most recent/complete financial data available
- If multiple financial years exist, sum for totals, show first/last for snapshot
- CPI/SPI from the most recent period in budget.csv
- Reference config.pmo_settings for governance notes

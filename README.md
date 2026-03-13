# Portfolio Management CLI

A Claude Code skill system for project and product portfolio management. Designed for PMs and Heads of Product — from first-timers to experienced portfolio managers.

## What It Does

Manages the full portfolio lifecycle through conversational AI:

- **Plan** — Strategy setting, project prioritization, scenario modeling, approval
- **Manage** — Register, update, pause, cancel, or fast-track projects
- **Review** — Health dashboards, governance reviews, budget tracking, EVM
- **Product Intelligence** — KPI cascades, value contribution, product reviews

## Entry Points

| Command | Purpose |
|---------|---------|
| `/pm` | Project portfolio management (planning, governance, projects) |
| `/pp` | Product portfolio intelligence (products, KPIs, value models) |

Users describe what they need in plain language — the system routes to the right workflow.

## Architecture

Three skill tiers orchestrated by two entry points:

```
/pm (Project Portfolio)
  ├── Tier 1: /bp-*  (11 skills) — Project-level workflows
  │   setup, project hub, financials, NPV, business case,
  │   teams, capacity, forecast, budget, EVM, import
  │
  └── Tier 2: /pf-*  (10 skills) — Portfolio-level workflows
      planning cycle, prioritization, allocation, health,
      review, scenarios, reports, snapshots, one-pagers, pipeline

/pp (Product Portfolio)
  └── Tier 3: /pp-*  (3 skills) — Product intelligence
      product registry, KPI cascades, product reviews
```

## Directory Structure

```
.claude/commands/     26 skill files (the entire system logic)
data/                 Live portfolio data (gitignored)
examples/             Sample data sets + demo dashboard
reports/              Dashboard + generated reports
```

## Data Layer

All skills read/write from `data/`:

| File | Purpose |
|------|---------|
| `config.json` | Portfolio settings, cost categories, governance thresholds |
| `strategy.json` | Strategic context with version history |
| `projects.csv` | Project registry (includes pipeline items) |
| `financials.csv` | Revenue & cost projections per project per year |
| `teams.csv` | Team registry with Agile/Traditional planning modes |
| `capacity.csv` | Sprint/phase capacity plans |
| `budget.csv` | Budget baselines and EVM tracking |
| `products.csv` | Product registry (independent of projects) |
| `product_kpis.csv` | KPI cascade definitions per product |
| `value_models.csv` | Business value models per product |

## Getting Started

1. Open the project in Claude Code
2. Run `/pm` — the system detects first-time use and guides setup
3. Import existing data (`/pm` → option 4) or register projects manually
4. Run the planning cycle to prioritize and approve your portfolio

## Key Behaviors

- **State detection** — reads all data files on entry, guides you to the right next step
- **Auto-recommendation** — prioritization engine analyzes strategy + financials + capacity and recommends rankings
- **Table-first input** — data-heavy skills show pre-filled tables, not one-at-a-time prompts
- **Central import engine** — `/bp-import` handles CSV/JSON parsing, fuzzy column matching, schema detection
- **Visual dashboard** — `reports/dashboard.html` renders portfolio state in-browser with no server needed

## Requirements

- [Claude Code](https://claude.ai/claude-code) CLI
- No runtime dependencies — the system is entirely prompt-driven skill files

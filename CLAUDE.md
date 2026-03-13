# Portfolio Management CLI

## Overview
A Claude Code skill system for project portfolio management. Designed for PMs and Heads of Product — from first-timers to experienced portfolio managers.

**Two entry points:**
- `/pm` — Project portfolio management (planning, project management, governance)
- `/pp` — Product portfolio intelligence (products, KPI cascades, value contribution, product reviews)

## Architecture

Four-option menu (when portfolio is in normal operating state):

```
1. Plan portfolio     — Planning cycle: Strategy → Capacity → Proposals → Prioritize → Approve
2. Manage projects    — Register, update, pause, cancel, fast-track, import
3. Review portfolio   — Health dashboard, governance reviews, budget tracking
4. Product value      — Connect product metrics to business value, product portfolio review
```

Internally orchestrates three skill tiers across two entry points:

- **Tier 1: `/bp-*`** — Project-level (setup, project hub, financials, NPV, business case, teams, capacity, forecast, budget, EVM, import)
- **Tier 2: `/pf-*`** — Portfolio-level (planning cycle, prioritization, allocation, health, review, scenarios, reports, snapshots, one-pagers)
- **Tier 3: `/pp-*`** — Product Portfolio Intelligence (product registry, KPI cascades, value contribution, product data import, product reviews)

### Smart behaviors
- **State detection**: `/pm` reads all data files on entry, detects where the user is in the process, and guides them to the right next step
- **Auto-recommendation**: Prioritization engine analyzes strategy + financials + capacity and recommends a ranking. User adjusts, not scores from scratch
- **Auto-detect review type**: Reviews auto-detect monthly vs quarterly based on last review date
- **Strategy persistence**: Strategy stored in `strategy.json` with version history, carried forward across cycles
- **Prerequisites enforced**: Planning requires 2+ projects. Tracking requires approved projects. Budget/EVM requires baselines.

## Directory Structure
```
.claude/commands/     — Skill files (2 wrappers + 11 BP + 10 PF + 3 PP)
data/                 — Shared data layer (config.json + strategy.json + 10 CSVs)
reports/              — Generated reports, business cases, one-pagers, product reviews
```

## Data Files
All skills read/write from `data/`:
- `config.json` — Portfolio settings, cost categories, governance settings
- `strategy.json` — Persistent strategic context (mission, pillars, objectives, financial targets, investment guidance) with version history
- `projects.csv` — Project registry (includes pipeline items as status=Pipeline)
- `financials.csv` — Revenue/cost projections per project per year
- `teams.csv` — Team registry with planning mode (Agile/Traditional)
- `capacity.csv` — Sprint/phase capacity plans
- `budget.csv` — Budget baselines and EVM tracking
- `pipeline.csv` — Pre-approval project ideas (legacy — new pipeline items use projects.csv with Pipeline status)
- `snapshots.csv` — Historical portfolio state snapshots
- `products.csv` — Product registry (independent of projects — for product-focused orgs)
- `product_kpis.csv` — KPI cascade definitions (Product KPI → Lever → Business KPI per product)
- `value_models.csv` — Business value models per product (cost savings, revenue uplift, sustainability)

## Key Workflows

### New user flow
1. `/pm` → detects first time → runs `/bp-setup` (portfolio configuration)
2. Offers data import or manual project registration
3. After 2+ projects → nudges to planning cycle
4. Planning cycle: Strategy → Capacity → Proposals → Auto-prioritize → Trade-offs → Approve
5. After approval → "Review portfolio" unlocks for ongoing governance

### Ongoing governance
- **Monthly**: Dashboard + auto-flag breaches + collect updates + action items
- **Quarterly**: Everything monthly + financial refresh + re-prioritize + strategic alignment + executive report

### Product value tracking (for product-focused companies)
1. `/pp-cascade add` → Register products
2. `/pp-cascade setup [product]` → Define KPI cascade (Product KPI → Lever → Business KPI → Value)
3. Import business case via `/bp-import` → Populates value_models.csv
4. `/pp-cascade update [product]` → PM updates product KPIs each review cycle
5. `/pp-review` → Portfolio-wide product review with value contribution analysis

### Mid-cycle changes
- Handled through "Manage projects" → pause, cancel, descope, or emergency inject
- Emergency injection includes portfolio impact assessment (what gets displaced)
- All changes trigger portfolio impact analysis before execution

## UX Patterns
- **Conversational, not technical** — users never need to know sub-command names
- **AI recommends, user adjusts** — prioritization, review type, scenarios are auto-generated
- **Table-first input**: Data-heavy skills show pre-filled tables and accept flexible input
- **Import engine**: `/bp-import` handles file discovery, CSV/JSON parsing, fuzzy column matching, schema detection, validation
- **Insights after every action**: Interpretation, risk flags, recommended next steps

## Code Style
- CSVs use standard format (comma-delimited, quote fields with commas)
- IDs: Projects = P001, Teams = T001, Products = PRD01
- Currency values stored as raw numbers, formatted on display
- Dates in YYYY-MM-DD format

## Key Conventions
- Always read data files before modifying — use read-modify-write pattern
- Never delete CSV rows — soft delete by setting status to Cancelled
- Config.json provides defaults for all calculable parameters
- Agile and Traditional teams coexist — normalized at portfolio level
- Cost categories from config, not hardcoded
- Escalation thresholds from config.pmo_settings

# Portfolio Management CLI — Backlog

All improvement ideas and feature requests for the portfolio management skill system.

**Priority Levels:**
- **P0** — Urgent / must do this week
- **P1** — Important / do soon
- **P2** — Explore later / someday

---

## V1 Tasks (Current — CLI Skill Files)

- [ ] `P1` Explicit project→objective mapping — Add a `strategic_objectives` field to projects.csv so coverage tracking is stored, not recalculated via semantic matching each time. Current approach is fragile (different sessions could map differently) and not auditable.
- [x] `P1` Auto-generate report + snapshot at end of planning & review workflows — implemented in pf-plan.md and pf-review.md. E2E tested with monthly review on 2026-03-12 using Olympus mock data. Report generation, snapshot save, and dashboard reference all verified.

---

## Product Roadmap

### V2: Web App + Service API (6-8 weeks)

Chat-first web app where AI responses render as interactive components (tables, charts, forms). Claude API runs skill logic server-side.

- [ ] `P1` Next.js frontend with shadcn/ui + Recharts for core portfolio views
- [ ] `P1` Service API layer (REST/tRPC) — extract business logic so any client can call it
- [ ] `P1` Database migration — replace CSVs with SQLite or Postgres
- [ ] `P1` AI layer — Claude API with skill files as system prompts
- [ ] `P1` Chat rendering — Vercel AI SDK (streaming + tool use)
- [ ] `P2` Auth — Clerk or NextAuth
- [ ] `P2` Role-based access (PM, Engineering Lead, CPO, CEO views)

### V3: Multi-Channel Access (2 weeks, after V2)

Telegram, Slack, email — same service API, different clients.

- [ ] `P1` Telegram bot — quick status, portfolio pulse, project updates
- [ ] `P1` Push alerts — cron job checking thresholds → sends Telegram/Slack messages (budget breach, SPI/CPI below threshold, review overdue)
- [ ] `P2` Inline actions — Telegram/Slack buttons for approvals, quick updates
- [ ] `P2` Slack bot — same architecture as Telegram, different SDK
- [ ] `P2` Email digests — weekly portfolio summary auto-sent

### V4: /pp as Separate Module + Value Bridge (8 weeks, after V3)

Product Portfolio Intelligence as a standalone module. Products ≠ Projects.

- [ ] `P1` Extract /pp data into its own database tables (products, KPI cascades, value models, product reviews)
- [ ] `P1` /pp service API — CRUD + KPI cascade logic
- [ ] `P1` /pp web UI — product cards, cascade visualization, review views
- [ ] `P1` Value bridge — link projects to products, track value delivery ("which projects are actually moving product KPIs?")
- [ ] `P2` /pp Telegram/Slack support (same pattern as V3)
- [ ] `P2` Combined dashboard — portfolio view showing both project health AND product value

### V5: Always-On AI Portfolio Advisor (4-6 weeks)

Proactive intelligence — surfaces insights you didn't ask for.

- [ ] `P1` Continuous monitoring — watch for velocity stalls, budget overruns, deadline risks before they become crises
- [ ] `P1` Proactive alerts with recommendations — "P003 has been at 60% for 3 weeks. At current velocity, it'll miss Aug deadline by 6 weeks. Here are 3 options."
- [ ] `P2` External signal integration — connect to market data, competitor moves, industry reports and map to portfolio decisions
- [ ] `P2` Strategy drift detection — "Over 3 quarters, your actual spend drifted from 60/25/15 to 80/15/5. You're becoming a single-bet portfolio."

### V6: Reverse Portfolio + Decision Journaling (4 weeks)

Outcomes-first planning and organizational learning.

- [ ] `P1` Reverse portfolio — "I need $8M ARR by EOY. What portfolio gets me there?" Work backward from outcomes to project composition
- [ ] `P1` Monte Carlo simulation — probability distribution of hitting goals given different portfolio compositions
- [ ] `P1` Decision journaling — after each cycle, auto-generate retrospective: "6 months ago you chose Scenario A. Here's what actually happened."
- [ ] `P2` Decision quality scoring — track prediction accuracy over time, build organizational calibration
- [ ] `P2` Portfolio-as-Code — version-controlled decisions, diff planning cycles, branch/revert strategy changes

### V7: Integration Layer (6 weeks)

Auto-updating portfolio — connects to where work actually happens.

- [ ] `P1` Jira/Linear — auto-pull sprint progress → update project % complete
- [ ] `P1` Finance tool — actual spend → auto-update budget burn
- [ ] `P2` GitHub — commits/PRs per project → velocity proxy
- [ ] `P2` HRIS — headcount changes → auto-update capacity
- [ ] `P2` CRM — revenue data → validate financial projections
- [ ] `P2` Calendar — auto-schedule reviews based on cadence

### V8: Cross-Company Benchmarking — SaaS Platform (8-10 weeks)

Network-effect moat. Individual tools are commodities; benchmarks are defensible.

- [ ] `P1` Anonymous benchmarking — "Your project failure rate is 25%. Industry average for your size is 18%."
- [ ] `P1` Estimation benchmarks — "API platform projects at similar companies take 40% longer than estimated."
- [ ] `P2` Investment mix benchmarks — "Companies in your segment invest 20% in Transformational. You invest 0%."
- [ ] `P2` Industry-specific insights — vertical-specific patterns and recommendations

---

## Other Creative Ideas (Unscoped)

- [ ] **Portfolio Copilot for Meetings** — Live meeting companion (Zoom/Teams transcript) that captures decisions, flags contradictions with data, generates action items, updates portfolio state in real-time
- [ ] **Stakeholder-Specific Views** — Same portfolio, different lenses: CEO gets 3 numbers on one page, IC gets "what should I work on this sprint and why it matters"
- [ ] **Natural Language Portfolio Queries** — Free-form questions: "What's our exposure if the API project fails?" answered from portfolio data across all channels
- [ ] **Predictive Analytics** — Estimation accuracy scoring, seasonal velocity patterns, project risk predictor based on historical patterns

---

## Summary Timeline

```
V1  CLI skill files                              (done)
V2  Web app + service API                        6-8 weeks
V3  Multi-channel (Telegram, Slack)              2 weeks
V4  /pp as separate module + value bridge        8 weeks
V5  Always-on advisor + predictive analytics     4-6 weeks
V6  Reverse portfolio + decision journaling      4 weeks
V7  Integration layer + auto-updating portfolio  6 weeks
V8  Cross-company benchmarking (SaaS platform)   8-10 weeks
                                                ─────────
                                        Total:  ~18 weeks (V2-V4 core)
                                                ~40 weeks (full vision)
```

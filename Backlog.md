# Portfolio Management CLI — Backlog

All improvement ideas and feature requests for the portfolio management skill system.

**Priority Levels:**
- **P0** — Urgent / must do this week
- **P1** — Important / do soon
- **P2** — Explore later / someday

---

## Tasks

- [ ] `P1` Explicit project→objective mapping — Add a `strategic_objectives` field to projects.csv so coverage tracking is stored, not recalculated via semantic matching each time. Current approach is fragile (different sessions could map differently) and not auditable.
- [x] `P1` Auto-generate report + snapshot at end of planning & review workflows — implemented in pf-plan.md and pf-review.md. E2E tested with monthly review on 2026-03-12 using Olympus mock data. Report generation, snapshot save, and dashboard reference all verified.

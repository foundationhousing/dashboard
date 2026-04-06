# Foundation Data Pipeline — Steve Sync
**Prepared:** April 6, 2026 | **Prepared by:** Ryan

---

## Agenda

1. Pipeline demo + how to access (5 min)
2. Current state of data — what's accurate, what's not (10 min)
3. Action items for Steve (10 min)
4. Construction carry costs — Steve's note on separating from pro formas (5 min)
5. FMG / FGCD financials — next phase (5 min)
6. Access & security — gating the dashboard (5 min)

---

## 1. What We Built

We now have a live, automated data pipeline:

| Component | URL / Location |
|-----------|---------------|
| **Live Dashboard** | https://foundationhousing.github.io/dashboard/ |
| **Database (Supabase)** | https://supabase.com/dashboard/project/cgipikbdlukxkbiybqkh |
| **Pipeline Code (GitHub)** | https://github.com/foundationhousing/foundation-data |
| **Audit Reports** | Generated each pipeline run → `07_DQ/` |

**How it works:**
- Rent roll auto-pulls from Google Sheets
- Pipeline builds canonical tables → dashboard views → HTML dashboard
- One command publishes everything: `python 00_ADMIN/publish.py`
- Auditor ("Chucky") runs automated data quality sweeps

**Steve's access:**
- Dashboard: open the URL above (currently public, auth coming)
- Supabase: Ryan to send invite to Steve's email
- GitHub: Ryan to add Steve to foundationhousing org

---

## 2. Current State of Data

### What's Accurate
| Table | Status | Source |
|-------|--------|--------|
| Rent roll / leases (AY 26-27) | **Current** — 318 rows, 189 signed | Google Sheets (auto-ingested) |
| Property registry | **Good** — 48 properties | SOT, manually maintained |
| Bed inventory | **Good** — 305 beds | SOT |
| NOI projections (v5) | **Good** — 42 properties, multi-year low/mid/high | Internal model |
| Construction scope + budget | **Good** — $9M hard costs across 18 reno properties | SOT |
| Capital stack (private debt + pref equity) | **Needs reconciliation** — see below | Foundation Loan Balances |
| Valuation (income approach) | **Directional** — $50.2M at ~7% cap | REO schedule |

### What Needs Work

**CRITICAL — Need Steve's Input:**

| Issue | Detail | What Steve Needs to Provide |
|-------|--------|---------------------------|
| **12 matured senior loans ($4.6M)** | Maturity dates Jan-Mar 2026. Likely refinanced but our schedule doesn't reflect it. | Current loan statements from CNB/Visions showing refi status, new terms, current balances |
| **Yves Hunziker balance** | SOT shows $318K. Ryan believes substantially paid down. | Confirm current balance from QBO or bank records |
| **Christian Widhalm balance** | SOT shows $121K. ~$70K in payments made. | Confirm current balance (~$51K expected) |
| **sot_financials_monthly is EMPTY** | No actual P&L data in the system. Can't do actual vs projected comparisons. | QuickBooks access or manual monthly close export |
| **Construction carry costs** | Per Steve's note: interest, principal, taxes during construction need to be separated from operating pro formas and added to construction budgets | Updated construction plan with carry/soft cost estimates by property |
| **Sumner construction loan extension** | Steve noted Sumner won't be tackled until May 2027. Loan dates need extending. | Confirm which Sumner loans, discuss with CNB |

**WARNING — Needs Attention:**

| Issue | Detail |
|-------|--------|
| 11 valuation vs projection NOI mismatches (>25% gap) | v5 projections are higher than valuation model — need to align |
| 719 E Genesee excluded from portfolio RE value | Correctly excluded (owned by 719 RGD LLC, not FA). FMG revenue from 719 tracked separately. |
| P012 (719 EG) still flagged as "Foundation" in properties table | Should be flagged as "managed, not owned" |
| Property P011 bed count mismatch (6 vs 7) | Reconcile between properties and beds table |
| 6 properties missing bed counts (P035-P040) | Land/lots — confirm if beds should be 0 or TBD |

---

## 3. Key Financial Metrics (Current)

### Portfolio (Foundation Assets — PropCo)

| Metric | Value | Notes |
|--------|-------|-------|
| RE Value (Income Approach) | $50,168,031 | Stabilized NOI / ~7% cap |
| RE Value (Book/Cost Basis) | $40,829,847 | Purchase + CapEx invested |
| Senior Debt (projected 9/1/26) | $27,730,851 | Includes ~$5.5M construction financing |
| Non-Senior Debt (true debt) | $1,524,122 | F&F loans + BTC |
| Total Debt | $29,254,973 | |
| LTV (Income) | 58.3% | |
| Loan-to-Cost | 67.9% | |
| Preferred Equity | $14,202,946 | Ryan $8.6M + Clay $3.5M + Steve $1.4M + Laney $544K + Greg $126K |
| Common Equity (Income, adj) | $5,032,584 | After 25% haircut |
| Common Equity (Book, adj) | -$1,971,054 | Underwater on cost basis |

### Operating Performance

| Metric | Current (AY 26-27) | Stabilized (AY 27-28) |
|--------|-------------------|----------------------|
| NOI | $1,698,561 | $3,099,452 |
| DSCR | **0.78x** | **1.43x** |
| Debt Yield | 5.8% | 10.6% |
| Signed Beds | 189 / 303 (62.4%) | — |

**Current DSCR is below 1.0x** — Foundation Assets is not covering debt service from current NOI. This is expected with 18 properties under renovation producing zero revenue, but it means the portfolio is cash-flow negative until stabilization.

---

## 4. Steve's Note — Construction Carry

Steve's email raised separating construction carry from operating pro formas. Specifically for 408 Euclid (adding 2-3 beds):

**Current state:** 408 is showing -$6,390 NOI in 26-27 (just opex while offline). But construction loan interest, property taxes during reno, and insurance aren't tracked as construction carry.

**What we need:** A `construction_carry` column or table that captures per-property:
- Interest on construction loans during renovation period
- Property taxes during construction
- Insurance during construction
- Carrying cost timeline (start → certificate of occupancy)

**This separates the "cost to build" from "cost to operate" and gives a cleaner picture of actual construction ROI vs operating margins.**

---

## 5. Next Phase — FMG + FGCD (OpCo)

The dashboard currently covers Foundation Assets (PropCo) only. To prepare for Jadian:

**Foundation Management Group (FMG):**
- Revenue: management fees (% of rents per property), 719 EG lease income ($97.6K/yr), other
- Expenses: payroll (Laney, maintenance staff), office, software
- This becomes the "platform" revenue Jadian evaluates

**Foundation GC & Design (FGCD):**
- Revenue: construction contracts (internal + external)
- Expenses: labor, materials, subs
- Steve's point: 15-20% GC margins on construction feed the OpCo
- Has ~$1.1M in entity-level operating debt (LOCs, MCAs)

**To build this we need:**
- FMG P&L (even a rough one — last 12 months actuals from QBO)
- FGCD P&L (same)
- Management agreement terms (% fee per property)
- GC contract terms for internal projects

---

## 6. Access & Security

**Current state:** Dashboard is public (anyone with URL can view). Database requires Supabase login.

**Plan:**
- Short-term: Add basic password protection to the dashboard (JavaScript prompt or Supabase auth gate)
- Mid-term: Supabase Row Level Security is already enabled — team members get read access via invite
- GitHub: private repo (pipeline code), public repo (dashboard — will switch to private + auth)

**Steve needs:**
- Supabase invite (Ryan to send)
- GitHub org invite (Ryan to send from https://github.com/orgs/foundationhousing/people)

---

## Action Items Summary

| # | Item | Owner | Priority |
|---|------|-------|----------|
| 1 | Provide current CNB/Visions loan statements for 12 matured loans | Steve | **CRITICAL** |
| 2 | Confirm current balances: Yves Hunziker, Christian Widhalm | Steve | HIGH |
| 3 | Updated construction plan with carry costs by property | Steve | HIGH |
| 4 | Confirm Sumner construction loan extension plan | Steve | MEDIUM |
| 5 | QBO access or monthly close export (any 3 months) | Steve/Ryan | HIGH |
| 6 | FMG + FGCD rough P&L (last 12 months) | Steve | MEDIUM |
| 7 | Management agreement terms (% fee schedule) | Steve | MEDIUM |
| 8 | Ryan to send Supabase + GitHub invites to Steve | Ryan | LOW |

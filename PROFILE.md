# Campaign Success Tracker — Portfolio Profile

**One-liner:** A single-file dashboard that decides whether a field-engagement campaign paid for itself —
incremental lift, auto-derived attribution, and ROI/IRR/NPV against the bank-deposit hurdle — shown on
invented data, fully offline.

---

## 1. Abstract

Marketing campaigns are usually judged by whatever the sales team quotes the following month. Because
seasonality dominates the volume curve, "sales went up" proves nothing, and the finance question — *did the
programme beat simply parking the money?* — never gets answered. This artifact ports the whole counterfactual
model into a single `index.html`. Baseline is the same sales month last year; lift is actual minus baseline;
attribution is auto-derived from a control market by difference-in-differences rather than a guessed
retention rate; the attributed lift is valued at gross contribution, netted against phased investment, and
converted into ROI, ROMI, payback, IRR and NPV against a deposit-rate hurdle, with a colour-coded verdict.
The app renders each unit's computed metric object and recomputes aggregates (monthly totals, the combined
two-unit view, per-KPI insights) live at render time. Chart.js is inlined so the file runs with no network
and no backend, and the visual system follows a shared data-dense design spec: Fira typography, blue-and-amber
tokens, dark-first with a light toggle, sticky table headers, tabular numerals, and reduced-motion support.

## 2. Problem Framing

The business context is a two-line campaign review. A conglomerate runs field-and-dealer programmes in
distinct categories — cement (high contribution per tonne) and steel (thin margin, so far more lift is needed
to clear the same bar). Each programme had a budget, a target and six campaign months whose sales landed one
month later. The original workbook answered the "was it worth it" question in Excel; every review rebuilt it
by hand, and the rebuild is where rigour quietly dies. The artifact exists to make the method *unavoidable*
and *inspectable*: open one file, read the verdict, and follow it down to the month that caused it.

## 3. Related Practice

The tracker encodes established marketing-effectiveness practice rather than inventing it: the
**incremental-lift / counterfactual** approach (measuring lift over a same-season prior-year baseline),
**control-group / difference-in-differences attribution** (the share of lift the campaign can claim),
**contribution (not revenue) valuation**, **phased-investment netting**, and **capital-return framing** —
ROI, ROMI, payback period, IRR and NPV weighed against a risk-free hurdle (the bank FDR). The verdict bands
mirror how a finance review actually talks: green if the return beats deposit money, amber if positive but
short of it, red if the programme destroyed value.

## 4. Methodology & Data Design

Data is one inline `const DATA` object plus a `SOURCES` array (both fully synthetic). Each unit carries a
six-month campaign ledger (campaign month, the sales month it shows up in, baseline, target, actual), a
cash-flow schedule, a trailing-12-month audit series, and the computed headline metrics. The model, with its
formulas as documented in the Methodology tab:

- **Baseline** = the same *sales* month last year (seasonal counterfactual; campaign month `m` → sales month `m+1`).
- **Lift (MT)** = Actual − Baseline;  **Lift %** = Lift ÷ Baseline.
- **Attribution %** (auto) = (campaign growth − control growth) ÷ campaign growth, clamped to 0–100%.
- **Attributed lift (MT)** = Lift × Attribution %.
- **Price/MT** = revenue ÷ volume;  **CM %** = (revenue − cost) ÷ revenue;  **Contribution/MT** = Price × CM %.
- **Incremental revenue** = Attributed lift × Price;  **Incremental contribution** = Attributed lift × Contribution/MT.
- **Net contribution** = Incremental contribution − Investment (budget phased evenly across the 6 campaign months).
- **ROI** = Net ÷ Investment;  **ROMI** = Contribution ÷ Investment.
- **Payback (months)** = first month where cumulative cash flow turns ≥ 0;  **IRR** (monthly, then annualised) and
  **NPV @ hurdle** solved over the cash-flow schedule;  **Target achievement %** = actual ÷ target.
- **Verdict** = SUCCESS if ROI beats the annual bank FDR, PARTIAL if positive but below, UNDERPERFORMING if negative.

Two layers run in the browser: per-unit headline figures arrive precomputed inside `DATA` (they are the model's
output), while totals, the combined two-unit blend, and the per-KPI insight chips are recomputed from the stored
monthly rows on every render. `META` labels the units; `VERDICT` maps each verdict key to a label, colour and
text-ink choice.

## 5. Analytical Framework

The UI encodes a chain of custody for the decision: **counterfactual → lift → attribution → contribution
value → net of investment → return vs hurdle → verdict**, each step on its own tab (Methodology, Assumptions,
Data, Control Market, Analysis, Results, Sources). The filter (Both / Nexa Cement / Nexa Steel) is the
comparison primitive: the two units are deliberately built to disagree — cement clears the hurdle, steel does
not — so the same method produces opposite, defensible verdicts, and the combined tab shows how they net out.
Colour is never the only signal: every verdict badge pairs a hue with a word, and every chart pairs series with
legend labels and tooltips.

## 6. System Design

Plain HTML/CSS/vanilla JS with Chart.js **inlined** into the file (a vendored v4 UMD bundle) and the dataset
embedded as a JSON object — deliberately static, offline-first and deployable as one file. A small
`state = {f, t}` (unit filter, active tab) drives `render()`, which rebuilds the content region, destroys and
redraws only the dashboard charts. Chart colours come from the spec palette (slate baseline, light-blue target,
emerald net, amber ROI) plus each unit's identity colour; `Chart.defaults` are re-read from the live CSS tokens
on every theme change so axes, gridlines and tooltips stay legible in dark *and* light mode. The sticky top bar
carries the product mark, a synthetic-data chip, the unit filter, the tab nav and the theme toggle; tables use
sticky headers with right-aligned tabular numerals. It is static because the auditable method — not the
infrastructure — is the deliverable.

## 7. Findings & Design Decisions

The synthetic story is pointed on purpose: Nexa Cement's high contribution per tonne means modest lift clears
the hurdle and the campaign is a SUCCESS (ROMI ~1.5×, payback in month 4), while Nexa Steel's thin margin turns
an apparently larger percentage lift into a net loss and an UNDERPERFORMING verdict — the same budget, opposite
economics. Design choices that carry that: the **verdict banner** states the conclusion before any number;
**insight chips** in the hero give the four headline reads at a glance; **tabs** let a sceptic drill from verdict
to the exact control-market calculation; and a single **first-paint load-in stagger** (≤450 ms, once, skipped under
reduced motion) is the only entrance motion, with hover lift reserved for interaction.

## 8. Limitations & Validity

Everything is invented. **NexaGroup, Nexa Cement and Nexa Steel are fictional**; all volumes, prices, margins,
budgets, cash flows and returns were generated for this portfolio piece with realistic magnitude and the same
structure as the production model. No real organisation, ledger, customer or transaction appears, and nothing
here is an investment recommendation. Methodological caveats are real, not cosmetic: the prior-year baseline
assumes comparable seasonality; difference-in-differences assumes the control market is otherwise exchangeable
with the campaign market (a single control is a simplification); attribution is clamped 0–100% so it can never
be negative or >100%; and the IRR/NPV reflect the documented cash-flow convention. Headline metrics are shown
as computed by the model; the browser recomputes aggregates and the combined blend, not the underlying per-unit
solves.

## 9. Reproducibility & Deployment

```bash
open index.html          # double-click works; fully self-contained and offline
```

No install, no build, no server — Chart.js and the dataset are in the file. To run the method on your own
numbers, replace `const DATA` with an object of the same shape (per-unit `monthly`, `cashflow`, `results`,
`audit` blocks plus the computed fields). **Vercel:** Import the repo → Framework preset **Other** · Root `.` ·
build command *empty* · Output directory *empty*; the file is served as-is. Owner credit: **Built by Abir**.

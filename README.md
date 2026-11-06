# Campaign Success Tracker (portfolio demo)

Self-contained HTML dashboard that measures a field-engagement campaign with the
**incremental-lift (counterfactual)** method: baseline = the same sales month last year,
lift = actual − baseline, attribution auto-derived from a control market
(difference-in-differences), valued at gross contribution, netted against investment,
then ROI / ROMI / payback / IRR / NPV vs a deposit-rate hurdle.

> **All data in this repo is synthetic.** "Nexa Cement" and "Nexa Steel" are fictional
> business units of a fictional group ("NexaGroup"); volumes, prices, budgets and returns
> are invented for the demo. No real company, ledger or system is referenced.

## Tabs

Dashboard · Methodology · Assumptions · Data · Control Market · Analysis · Results · Sources —
each mirrors a sheet of the original spreadsheet model, so the workbook logic and the
web view agree line by line. Filter by unit (Both / Nexa Cement / Nexa Steel).

## Run / deploy

Static single file — no build, no backend, only aggregated results are published:

```bash
open index.html          # double-click works too
```

Vercel: import the repo, framework preset **Other**, root `.`, no build command.

## Structure

```
index.html   page + inlined Chart.js + embedded demo dataset (const DATA / SOURCES)
```

`DATA` is generated as a JSON blob at the top of the page script; replacing it with your
own object of the same shape is all that is needed to run the model on real numbers.

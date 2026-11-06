# Campaign Success Tracker (NexaGroup demo)

**One-liner:** A single-file dashboard that decides whether a marketing campaign actually
paid for itself — incremental lift, auto-derived attribution, and ROI/IRR against the
bank-deposit hurdle — shown here on invented data.

## Problem

Field-and-dealer campaigns are usually judged by whatever the sales team quotes the next
month. Because seasonality dominates the volume curve, "sales went up" proves nothing, and
the finance question — did the programme beat simply parking the money? — never gets
answered. The model lived in Excel, so every review re-built it by hand.

## Solution

Port the whole counterfactual model to the browser: baseline = same sales month last year,
lift = actual − baseline, attribution auto-calculated difference-in-differences against a
control market, valued at contribution (not revenue), netted against the phased
investment, then converted into ROI, ROMI, payback, IRR and NPV with a colour-coded verdict.
Two business units can be compared or viewed together. Everything ships as one HTML file.

## Features

* Verdict banner per unit (success / partial / underperforming) vs the risk-free hurdle.
* Monthly tracker: baseline, target, actual, lift, lift %, attributed contribution, net, monthly ROI.
* Control-market tab that derives attribution instead of hard-coding a retention guess.
* Cash-flow schedule with cumulative position, payback month, IRR (monthly + annualised) and NPV.
* Combined view that blends both units' investment, lift and ROI.
* Methodology + sources tabs so the maths is auditable inside the deliverable; charts by Chart.js.

## Tech stack

Plain HTML/CSS/JavaScript (no framework, no backend) · inlined Chart.js 4 UMD bundle ·
embedded JSON dataset · bisection IRR solver · one static file, offline-friendly.

## Demo disclaimer

Synthetic demo data only. NexaGroup, Nexa Cement and Nexa Steel are fictional; every volume,
price, margin, budget and return figure was generated for this portfolio piece (realistic
magnitude, same structure as the production model). No real organisation, ledger, customer
or transaction appears.

## Run locally

```bash
open index.html      # or: npx serve .
```

## Deploy on Vercel

Import the repo → **Framework Preset: Other** (static) · Root Directory `.` ·
Build Command *empty* · Output Directory *empty*. No build step is required.

# Product Funnel Analytics — GA4 & Firebase

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/zeeshan9595/product-funnel-analytics/blob/main/product_funnel_analytics.ipynb)
[![Live dashboard](https://img.shields.io/badge/Live-dashboard-2a78d6)](https://zeeshan9595.github.io/product-funnel-analytics/)

**Who uses this:** a product or growth team deciding which step of the user journey to fix first, and for which users.

**Data:** Google's public BigQuery exports, queried with the same SQL you would run on a company's own export.

| Source | Dataset | Used for |
|---|---|---|
| GA4 (web) | `bigquery-public-data.ga4_obfuscated_sample_ecommerce` (Google Merchandise Store, Nov 2020 – Jan 2021) | Purchase funnel, device / channel / country segments, revenue |
| Firebase (app) | `firebase-public-project.analytics_153293282` (Flood-It mobile app, 2018) | Install → activation funnel, weekly retention cohorts, in-app purchase |

## What the analysis does

1. **Closed funnel** — visit → view item → add to cart → checkout → payment → purchase; a user counts only if every earlier step happened first.
2. **Segment diagnosis** — step conversion by device, channel and country, to find *where* and *for whom* the funnel breaks.
3. **Significance test** — two-proportion z-tests on the desktop vs mobile gap, overall and at the step where the gap is largest.
4. **Time to convert** — share of buyers who purchase in one sitting vs come back later.
5. **App activation funnel** — install → first level → completed level → returned another day → in-app purchase.
6. **Weekly retention cohorts** — heatmap by install week.
7. **Activation vs retention** — week-2 retention by day-0 activity (the "aha moment" question).

## Key findings

| | |
|---|---|
| Web visitors (Nov 2020 – Jan 2021) | 267,116 |
| Visit → purchase | 0.88% (2,356 buyers, $362k revenue, AOV $86) |
| App installs (Firebase) | 4,319 · week-1 retention 21.5%, week-4 7.3% |

1. **The funnel breaks at product discovery, not checkout.** Only 22.9% of visitors view a product and 18.1% of viewers add to cart, the weakest step. Once at checkout, 69.7% add payment and 81.2% of those buy. Fix search, navigation and product pages before the checkout form.
2. **Device is not the problem.** Desktop 0.87% vs mobile 0.90% visit → purchase (two-proportion z-test, p = 0.44); no step differs significantly. A useful negative result: a mobile-checkout project would not move conversion here.
3. **Channel quality differs 2×.** Referral converts 1.16% at $1.87 per visitor; paid search 0.59% at $0.83. Check landing pages and keyword intent before raising paid spend.
4. **51% of buyers purchase within an hour of first view; 18% take more than a week.** Re-engagement is part of the funnel.
5. **App: the first step is the leak.** Only 47.6% of installers start a level. Users completing 6+ levels on day one retain at 24% in week 2 vs 10.9% for those completing none (correlation, not causation), so the next step is an onboarding A/B test.

*"(data deleted)" in the channel table is an obfuscation artefact in Google's sample and is excluded from conclusions.*

## Why it matters for a fintech / digital-wallet product

The event model is the same: install → sign-up → KYC → first top-up → first transaction. The closed-funnel SQL, segment cuts, significance tests and cohort tables carry over unchanged to a wallet's Firebase export.

## Run it

1. Open the notebook in Colab (badge above).
2. Set `PROJECT_ID` to any Google Cloud project (the free BigQuery sandbox works). Every query is dry-run first and capped at 20 GB scanned.
3. Run all. The last cell writes `dashboard_data.json`, which feeds `index.html`.

**Stack:** BigQuery SQL · Python (pandas, SciPy, statsmodels) · Chart.js · GitHub Pages

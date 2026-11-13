# Product case file: fixing the add-to-cart leak

A worked example of a product owner's job from problem to post-launch review, on real data.

**Product:** Google Merchandise Store (web).
**Data:** Google's public GA4 BigQuery export, November 2020 to January 2021, as analysed in my [product funnel analytics project](https://github.com/zeeshan9595/product-funnel-analytics).
**What is real and what is not:** every number in sections 1 and 2 comes from that data. The hypotheses, scores, requirements and plan are my own product judgement. I have no user research, cost data or engineering estimates for this store, so those parts are marked as assumptions to validate.

---

## 1. The problem

Four in five people who look at a product leave without adding anything to the cart. That step loses more users than any other in the purchase funnel.

| Step | Users | Share of visitors | Conversion from previous step |
| --- | --- | --- | --- |
| Visit | 267,116 | 100.0% | |
| View item | 61,252 | 22.9% | 22.9% |
| Add to cart | 11,114 | 4.2% | 18.1% |
| Checkout | 4,167 | 1.56% | 37.5% |
| Payment info | 2,903 | 1.09% | 69.7% |
| Purchase | 2,356 | 0.88% | 81.2% |

Revenue in the period was $362,165, about $154 per buyer.

**What the data rules out**

- **It is not a mobile problem.** Visit-to-purchase is 0.87% on desktop and 0.90% on mobile (p = 0.44). Add-to-cart is 18.1% on desktop and 18.3% on mobile.
- **It is not a payment problem.** 81.2% of users who enter payment details complete the purchase.
- **Traffic quality matters.** Referral visitors convert at 1.16% ($1.87 per visitor); paid search at 0.59% ($0.83).

**Problem statement**

> Shoppers who view a product on the Google Merchandise Store do not add it to their cart 82% of the time, on every device. We do not yet know why. Closing part of this gap is the largest single lever on revenue in the current funnel.

---

## 2. Opportunity sizing

Lifting add-to-cart by 2 points is worth about $40,000 a quarter, more than the same improvement at any other step.

Working, using the quarter's real volumes and holding later steps constant:

| Improvement | Extra buyers per quarter | Extra revenue per quarter | Revenue uplift |
| --- | --- | --- | --- |
| Add to cart 18.1% to 20.1% | 260 | $39,900 | 11.0% |
| View item 22.9% to 24.9% | 205 | $31,600 | 8.7% |
| Checkout 37.5% to 40.5% | 189 | $29,000 | 8.0% |
| Payment info 69.7% to 72.7% | 101 | $15,600 | 4.3% |

Example for the first row: 61,252 viewers x 2 points = 1,225 extra carts. 21.2% of carts become purchases (2,356 / 11,114), so 260 extra buyers. At $154 each, about $39,900.

**Caveats**

- This assumes extra carts convert like existing ones. Marginal users are usually less committed, so treat $40,000 as a ceiling.
- The quarter includes the holiday season. An ordinary quarter will be smaller.
- A more cautious 1-point lift is worth about $20,000 a quarter.

---

## 3. Users and hypotheses

The data shows where users leave, not why. These are hypotheses to test, not findings.

**Who views a product without adding to cart (assumed segments)**

| Segment | Likely behaviour | What they need |
| --- | --- | --- |
| Browsers | Curious about Google merchandise, no intent to buy today | A reason to come back |
| Comparers | Looking at several items before choosing | Easy comparison, clear price |
| Blocked buyers | Want the item but hit an obstacle | The obstacle removed |
| Low-intent paid traffic | Arrived from an ad, product was not what they expected | Better targeting, not a better page |

**Hypotheses for the blocked buyers**

| # | Hypothesis | Evidence that would support it |
| --- | --- | --- |
| H1 | Shipping cost and delivery time are unknown until checkout | Low add-to-cart overall, plus a second large drop at checkout (62.5% leave) |
| H2 | Size or variant is out of stock or unclear | Add-to-cart lower on apparel than on single-variant items |
| H3 | Product pages lack reviews or enough photos to build confidence | Add-to-cart lower on higher-priced items |
| H4 | The add-to-cart button is below the fold or unclear | Scroll depth and click maps |
| H5 | Paid-search visitors land on products that do not match the ad | Add-to-cart lower for paid search than for referral |

**Validation before building anything (two weeks)**

1. Split add-to-cart rate by product category, price band and traffic channel in the existing data. This tests H2, H3 and H5 at no cost.
2. Check stock availability logs against product views for H2.
3. Run five to eight short user interviews or unmoderated tests on two product pages.
4. Add an exit survey on product pages: "What stopped you adding this today?"
5. Review session recordings or click maps for H4.

**Decision rule:** a hypothesis moves forward only if at least two sources support it.

---

## 4. Options and prioritisation

Showing delivery cost and time on the product page scores highest. Scores are my estimates and should be replaced with the team's.

RICE = Reach x Impact x Confidence / Effort. Reach is product viewers per quarter affected. Impact uses the standard scale (0.25 minimal, 0.5 low, 1 medium, 2 high). Effort is person-weeks.

| Option | Hypothesis | Reach | Impact | Confidence | Effort | RICE |
| --- | --- | --- | --- | --- | --- | --- |
| A. Delivery cost and date on the product page | H1 | 61,000 | 1 | 50% | 3 | 10,200 |
| B. Move add-to-cart above the fold, sticky on scroll | H4 | 61,000 | 0.5 | 50% | 2 | 7,600 |
| C. Stock and size clarity, with back-in-stock alerts | H2 | 25,000 | 1 | 50% | 4 | 3,100 |
| D. Reviews and richer photos | H3 | 61,000 | 1 | 30% | 8 | 2,300 |
| E. Tighten paid-search landing pages | H5 | 15,000 | 1 | 50% | 2 | 3,800 |

**Decision:** build A first, with B alongside as a low-cost design change tested separately. E goes to the marketing team as it needs no product build. C and D wait for the validation results.

**Why A:** it reaches every product viewer, it addresses the two largest drops at once (add-to-cart and checkout), and it is cheap to test.

**What would change the decision:** if category analysis shows the leak is concentrated in apparel, C moves to the top.

---

## 5. Product requirements

### Delivery cost and date on the product page

**Goal:** raise the share of product viewers who add to cart from 18.1% to at least 19.6%, without lowering checkout conversion.

**Non-goals**

- Changing delivery prices or carriers.
- Redesigning the product page.
- Changes to the checkout flow.

**Users:** shoppers viewing any product page, on any device.

**User stories and acceptance criteria**

**Story 1.** As a shopper, I want to see the delivery cost before I add an item to my cart, so that I am not surprised at checkout.

- The product page shows a delivery cost, or "Free delivery over $X", near the price.
- The cost shown matches the cost charged at checkout for the same destination in every test case.
- If the destination is unknown, the page shows the lowest standard rate with "from".

**Story 2.** As a shopper, I want to see when my order would arrive, so that I can decide whether it will come in time.

- The page shows an estimated delivery date range based on the shopper's country.
- The estimate updates if the shopper changes country.
- If no estimate is available, the element is hidden, never blank or wrong.

**Story 3.** As a shopper outside the delivery area, I want to be told early, so that I do not waste time.

- If the store does not deliver to the shopper's country, the page says so and the add-to-cart button is replaced by a clear message.

**Story 4.** As the product owner, I want every view and interaction tracked, so that I can measure the effect.

- Events fire for: delivery information shown, country changed, add to cart.
- Events carry the experiment group.
- Tracking is verified against server-side orders before the test starts.

**Data and dependencies**

- Delivery rates and service areas from the fulfilment system.
- Visitor country from the existing geolocation.
- Analytics events added to the current GA4 setup.

**Open questions**

- Does a rates service exist that the page can call, and how fast is it?
- Which countries does the store deliver to today?
- Is there a free-delivery threshold?

**Out of scope for version 1:** postcode-level estimates, express delivery options, delivery information in search results.

---

## 6. Success metrics and experiment plan

**Metric hierarchy**

| Level | Metric | Baseline | Target |
| --- | --- | --- | --- |
| Primary | Add-to-cart rate among product viewers | 18.1% | 19.6% or higher |
| Secondary | Visit-to-purchase rate | 0.88% | Up |
| Secondary | Revenue per visitor | $1.36 | Up |
| Guardrail | Cart-to-checkout rate | 37.5% | No lower than 36.5% |
| Guardrail | Product page load time | Current | No more than 5% slower |
| Guardrail | Delivery-related support contacts | Current | No increase |

The cart-to-checkout guardrail matters: showing delivery cost earlier should move the "bad surprise" forward, so checkout conversion should hold or improve. If it falls, the change only moved the drop.

**Experiment design**

- **Type:** A/B test, 50/50, randomised by user.
- **Control:** current product page. **Treatment:** product page with delivery cost and date.
- **Hypothesis:** treatment raises add-to-cart rate by at least 1.5 points.
- **Sample size:** 10,669 product viewers per group (5% significance, 80% power, baseline 18.1%).
- **Duration:** the store sees about 4,660 product viewers a week, so 21,338 users takes about 4.6 weeks. Run for 5 full weeks to cover whole weekly cycles.

| Detectable lift | Users per group | Weeks needed |
| --- | --- | --- |
| 1.0 point | 23,764 | 10.2 |
| 1.5 points | 10,669 | 4.6 |
| 2.0 points | 6,061 | 2.6 |

**Rules agreed before the test starts**

1. Check the split is 50/50 in the first two days. Stop if it is not.
2. Do not stop early because results look good.
3. Avoid the holiday peak, or run through it fully in both groups.
4. Ship if the primary metric is significantly up and no guardrail is breached.
5. Do not ship if the primary metric is flat. Record what was learned and move to the next option.

---

## 7. Roadmap, launch and review

**Roadmap (now, next, later)**

| Horizon | Work | Outcome sought |
| --- | --- | --- |
| Now (weeks 1-2) | Validation: category, price and channel splits; interviews; exit survey | Hypotheses confirmed or dropped |
| Now (weeks 3-5) | Build delivery information; set up tracking; QA | Feature ready for test |
| Next (weeks 6-10) | A/B test of delivery information; separate test of button placement | Decision to ship or stop |
| Next (weeks 11-12) | Roll out the winner; post-launch review | Add-to-cart at 19.6% or higher |
| Later | Stock and size clarity; reviews and photos; then the visit-to-view step | Next largest leak addressed |

**Launch checklist**

- [ ] Acceptance criteria pass on desktop, mobile and tablet.
- [ ] Delivery cost on the page matches checkout in every test country.
- [ ] Events verified against server-side orders.
- [ ] Rollback switch tested.
- [ ] Support team briefed on the change.
- [ ] Dashboard live with primary, secondary and guardrail metrics.
- [ ] Rollout staged: 10%, then 50%, then 100%, with a check at each stage.

**Risks**

| Risk | Likelihood | Impact | Response |
| --- | --- | --- | --- |
| Shown delivery cost differs from checkout | Medium | High | Single source for rates; automated comparison test |
| Showing cost early lowers add-to-cart | Medium | Medium | That is what the test measures; watch revenue per visitor, not carts alone |
| Rates service slows the page | Low | Medium | Load delivery information after the main content; cache by country |
| Holiday season distorts the result | Medium | Medium | Time the test outside the peak |
| Wrong estimate creates complaints | Low | High | Hide the estimate when confidence is low |

**Post-launch review (to complete four weeks after full rollout)**

1. Did the primary metric move, and by how much against the target?
2. Did any guardrail move?
3. Was the effect the same across devices, channels and countries?
4. Which hypothesis was confirmed or rejected?
5. What did we get wrong in sizing, scope or timing?
6. What is the next largest leak now?

---

## 8. A product owner's working week

The case above is the output. This is the routine that produces it.

**Daily**

- Check the product dashboard: yesterday's funnel, any metric outside its normal range.
- Clear blockers for the team: answer questions on stories, make scope calls.
- Refine one or two backlog items so they are ready to build.
- Review anything shipped: does it meet the acceptance criteria?

**Weekly**

- Review the funnel and segment trends, not single days.
- Read customer feedback: support tickets, survey responses, reviews.
- Check running experiments against the pre-agreed rules.
- Re-rank the backlog with any new evidence.
- Sprint planning or review, depending on the week.

**Monthly**

- Update the roadmap and say what changed and why.
- Re-size the top opportunities with fresh data.
- Review last month's launches against their targets.
- Look at one thing outside the product: a competitor, a new entrant, a change in the market.

**Quarterly**

- Revisit the product goals and the main metric.
- Write up what was learned, including what did not work.
- Agree the next quarter's outcomes, not a list of features.

**The artefacts a product owner keeps current**

| Artefact | Purpose | In this case file |
| --- | --- | --- |
| Problem statement | One agreed description of what is wrong | Section 1 |
| Opportunity sizing | Why this, why now | Section 2 |
| Hypothesis log | What we believe and how we will know | Section 3 |
| Prioritised backlog | What comes first and why | Section 4 |
| Requirements and stories | What "done" means | Section 5 |
| Metrics and experiment plan | How success is judged | Section 6 |
| Roadmap and risk log | When, and what could go wrong | Section 7 |
| Post-launch review | What we learned | Section 7 |

---

_Data: Google Merchandise Store GA4 sample dataset, public on BigQuery. Analysis and product plan by Zeeshan Azeem Khalid. This is a portfolio exercise and has no connection to Google._

# Case Write-Up: Product Transition Risk Analysis
## Fashion & Beauty Startup — Makeup Supply Chain

**Analyst:** Boda | **Date:** May 2025 | **Dataset:** 100 SKUs, 3 categories, 5 suppliers

---

## Problem

A fashion and beauty startup managing a 100-SKU makeup portfolio faces a product lifecycle transition across multiple categories. Without a structured transition plan, the company risks two simultaneous failure modes: running out of end-of-life stock before replacement products are available (stockout), or holding excess end-of-life inventory that ties up working capital and warehouse space (overstock).

The supply chain team has no formal visibility into which SKUs are approaching end-of-life, how much runway each has at current sell-through rates, or which suppliers pose the highest risk during the transition window.

---

## Analysis

**Step 1 — SKU Classification**

A two-signal classification model was applied to all 100 SKUs using availability score and defect rate as the primary indicators of lifecycle stage. SKUs were segmented into three groups: EOL (requires immediate phase-out), Phase-out (transition planning required), and Active (stable).

Result: 14 SKUs classified as EOL, 45 as Phase-out, 41 as Active. 59% of the portfolio requires active transition management.

**Step 2 — Risk Scoring**

A composite risk score was calculated for each SKU:

```
risk_score = defect_rate x (100 / (availability + 1)) x (lead_time / 10)
```

This formula amplifies risk for SKUs that are simultaneously deteriorating in quality, declining in availability, and sourced from suppliers with long lead times — the combination most likely to cause a supply disruption. SKU52 and SKU45 ranked highest with scores of 116.7 and 112.7 respectively.

**Step 3 — Days of Supply**

Days of supply was calculated for each SKU as stock level divided by the daily sell-through rate. 74 of 100 SKUs fall below the 7-day critical threshold, with a portfolio median of only 3.5 days. This indicates last purchase order cutoffs for EOL SKUs must be actioned within 2 weeks to prevent unplanned stockouts.

**Step 4 — Transition Timeline**

Since the dataset contains no historical date column, a 6-month transition schedule was engineered from operational data. Lead times and days-of-supply were used to calculate four milestones per SKU: last PO cutoff, channel depletion, EOL date, and new SKU ramp start. The top 12 highest-risk SKUs are visualized on a Gantt chart spanning May to August 2025.

**Step 5 — Supplier Dependency**

Supplier 1 holds 27% of all SKUs — the largest concentration in the portfolio — and 63% of its SKUs are in EOL or Phase-out status. This dual exposure (volume concentration + high transition rate) creates single-source risk: any disruption from Supplier 1 during the transition window would impact 17 SKUs simultaneously. Supplier 2 carries the highest transition exposure at 68.2%.

**Step 6 — Logistics Optimization**

Transportation mode analysis shows Sea at the lowest average logistics cost ($418) with a 7.1-day average shipping time, while Air costs $562 with a 5.1-day average. Road offers the best cost-speed balance at $553 and 4.7 days. For the transition window, Road is recommended as the default mode, with Air reserved only for SKUs with days-of-supply under 7 days requiring emergency replenishment.

---

## Recommendations

**Immediate (0–2 weeks)**

Issue last purchase orders for the 3 highest-risk EOL SKUs — SKU52, SKU45, and SKU29 — before their calculated PO cutoff dates of May 6, May 4, and May 3 respectively. Prioritize pending quality inspections for all 14 EOL-classified SKUs before executing final replenishment to avoid receiving defective stock.

**Short-term (2–6 weeks)**

Dual-source the 3 EOL SKUs currently sole-sourced from Supplier 1 (SKU52, SKU29, SKU54). Begin new SKU ramp planning for the 8 EOL SKUs with EOL dates before June 15. Switch default carrier to Road for all non-urgent transition shipments to reduce logistics cost versus current Air usage.

**Medium-term (6–12 weeks)**

Complete the phase-out of the remaining 45 phase-out SKUs using the Gantt milestone framework. Establish a defect rate threshold policy — any SKU exceeding 3.5% defect rate triggers automatic EOL review — to catch future transitions earlier. Rebalance supplier portfolio to reduce Supplier 1 concentration from 27% to below 20%.

---

## Impact

Applying the run-out schedule derived from this analysis to the 14 EOL SKUs, synchronized with the 6-month new SKU ramp timeline, reduces simulated end-of-life inventory excess by approximately 30% compared to an unmanaged phase-out. The risk scoring model prioritizes attention on the 25 highest-risk SKUs (top quartile), enabling the supply chain team to focus transition resources where disruption probability is highest.

---

*Tools used: Python (pandas, matplotlib, seaborn), interactive HTML dashboard (Chart.js)*  
*Dataset: supply_chain_data.csv via Kaggle — Fashion & Beauty Startup Supply Chain*

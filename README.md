# Supply Chain Product Transition Risk Analysis

**Product lifecycle transition analysis for a fashion & beauty startup makeup supply chain.**  
Identifies end-of-life SKUs, scores transition risk, models run-out timelines, and surfaces supplier dependency risks across 100 SKUs and 3 product categories.

---

## Project Summary

Consumer goods companies face significant operational risk during product transitions — excess end-of-life inventory, unplanned stockouts, and supplier disruptions can all derail a launch. This project applies a structured supply chain analytics framework to a real-world makeup startup dataset to answer three key questions:

1. Which SKUs need to be phased out, and how urgently?
2. What does the 6-month transition timeline look like?
3. Where are the supplier and logistics risks during the transition window?

---

## Key Findings

| Finding | Detail |
|---|---|
| 59% of SKUs require transition action | 14 EOL + 45 Phase-out out of 100 total |
| Supplier 1 is a concentration risk | 27% of all SKUs, 63% in EOL or Phase-out |
| Median days of supply is 3.5 days | 74 SKUs below the 7-day critical threshold |
| 36% of SKUs failed quality inspection | 41% still pending — understates defect risk |
| Sea transport is lowest cost | $418 avg vs $562 for Air — optimal for non-urgent transitions |

---

## Repository Structure

```
supply-chain-transition/
├── supply_chain_data.csv                    # Source dataset (100 SKUs, 24 columns)
├── product_transition_analysis.ipynb        # Python analysis notebook (all 4 phases)
├── product_transition_risk_dashboard.html   # Interactive HTML dashboard (no install needed)
├── transition_risk_register.csv             # Output: full 100-SKU risk register ranked by score
└── README.md
```

---

## Methodology

### Phase 1 — EOL Identification & Risk Scoring

SKUs are classified into three transition states using two primary signals:

- **Availability score** (0–100): low availability signals declining demand or supply issues
- **Defect rate** (% defective units): high defect rates are characteristic of late-lifecycle products

**Classification rules:**

| Status | Condition |
|---|---|
| EOL | Availability < 10, OR (Defect rate > 3.5 AND Availability < 30) |
| Phase-out | Defect rate > 3.0 OR Availability < 30 |
| Active | All other SKUs |

**Risk score formula:**

```
risk_score = defect_rate x (100 / (availability + 1)) x (lead_time / 10)
```

This composite score weights three signals: quality deterioration, market availability decline, and lead time exposure. Higher scores indicate more urgent transition action.

**Days of supply:**

```
days_of_supply = stock_level / (monthly_units_sold / 30)
```

Represents how many days of inventory remain at the current sell-through rate.

---

### Phase 2 — Transition Timeline Engineering

The source dataset contains no date column. Transition milestones are derived from supply chain operational data — specifically lead times and days-of-supply — to construct a realistic 6-month run-out schedule.

| Milestone | Derivation |
|---|---|
| Last PO cutoff | T + (lead_time / 2) days |
| Channel depletion | T + lead_time days |
| EOL date | T + lead_time + min(days_of_supply, 60) days |
| New SKU ramp start | EOL date + 14 days |

T = analysis reference date (2025-05-01). The Gantt chart visualizes the top 12 highest-risk SKUs across this timeline.

---

### Phase 3 — Supplier Dependency & Logistics Analysis

**Supplier concentration risk** is measured by the share of each supplier's SKUs that are in EOL or Phase-out status. A supplier with high transition exposure and a large SKU share represents a dual risk — volume and quality.

**Transportation mode analysis** evaluates the cost vs. speed tradeoff across Road, Air, Rail, and Sea modes to recommend the optimal carrier strategy during the transition window. Air is reserved for stockout-risk SKUs only.

---

### Phase 4 — KPI Dashboard

Three core transition KPIs are tracked:

| KPI | Definition | Current value |
|---|---|---|
| Fill rate | Average availability score across portfolio | 48.4 / 100 |
| Days of supply | Median days of inventory at current sell-through | 3.5 days |
| Defect rate | Average % defective units across all SKUs | 2.28% |

---

## Dashboard

`supply_chain_dashboard.html` is a fully self-contained interactive dashboard. Open it in any browser — no server, installation, or login required.

**Dashboard sections:**

- KPI summary cards
- SKU risk heatmap (filterable by transition status)
- Transition status breakdown by product category
- Days of supply distribution
- Inspection results summary
- Transition Gantt for top 12 risk SKUs
- Supplier dependency cards and exposure chart
- Transport mode cost vs. speed scatter
- Sortable risk register table (top 10 SKUs)
- Key findings and recommendations

The dashboard can be hosted as a static page on GitHub Pages with no configuration.

---

## How to Run the Notebook

**Requirements:**

```
pandas
numpy
matplotlib
seaborn
```

Install with:

```bash
pip install pandas numpy matplotlib seaborn
```

**Steps:**

1. Place `supply_chain_data.csv` in the same directory as the notebook
2. Open `supply_chain_transition_analysis.ipynb` in Jupyter
3. Run all cells (Kernel > Restart & Run All)
4. Charts are saved as `chart_01_*.png` through `chart_06_*.png`
5. Risk register is exported as `transition_risk_register.csv`

---

## Dataset

**Source:** [Supply Chain Analysis — Kaggle](https://www.kaggle.com/datasets/harshsingh2209/supply-chain-analysis)

A real-world supply chain dataset from a fashion and beauty startup. Contains 100 SKU-level records across 24 columns including inventory, demand, supplier, logistics, and quality data.

**Key columns used in this analysis:**

| Column | Used for |
|---|---|
| SKU | SKU identifier |
| Product type | Category segmentation (skincare, haircare, cosmetics) |
| Availability | EOL classification, fill rate KPI |
| Defect rates | Risk scoring, EOL classification |
| Stock levels | Days-of-supply calculation |
| Number of products sold | Days-of-supply calculation |
| Lead times | Risk scoring, transition timeline engineering |
| Manufacturing lead time | Ramp timeline modeling |
| Order quantities | Transition scheduling |
| Supplier name | Supplier dependency analysis |
| Inspection results | Quality risk flagging |
| Transportation modes | Logistics cost analysis |
| Shipping times | Transport mode tradeoff |
| Costs | Logistics cost analysis |

---

*Analysis reference date: 2025-05-01 | Python 3.x | Dataset: supply_chain_data.csv*

# Identifying Patterns in Global E-Commerce Sales
### Customer segmentation of a UK online retailer with RFM and K-Means

**Course:** DSAI501 - How To Use Big-Data and AI to Make Better Business Decisions (ML and Data Science)
**Professor:** Hunter Yan
**Problem statement:** Option C - Identifying Patterns in Global E-Commerce Sales
**Group members:** `Lars Nicolas Godoy (BMT28046)` · `Prem Jasani (BMT28115)` · `Radhika Gupta (BMT28063)` · `Rishi (BMT28067)`

---

## What this project does

We take two years of raw transactions from a UK based online gift retailer (the UCI *Online Retail II* dataset,
1,067,371 rows), clean them at scale, engineer customer-level **RFM** features, and segment customers with
**K-Means** into four actionable groups. We add a **market-basket** analysis to surface cross-sell pairs. The goal is
not the model but the translation into decisions a retention and merchandising team can act on.

### Headline findings
- Cleaned **1,067,371 → 776,577** transactions (5,852 customers, £17.07M revenue).
- **Champions are ~20% of customers but ~73% of revenue.** A third of the base is effectively dormant.
- A valuable **At-Risk** segment (median £1,455 spent, ~6 months inactive; ~£2.9M of historic revenue) is the prime win-back target.
- Market-basket rules reveal strong cross-sell pairs (e.g. colour variants of the same line, lift > 20).

## Repository structure
```
.
├── notebooks/
│   └── online_retail_segmentation.ipynb   # full workflow, outputs pre-rendered
├── report/
│   ├── Report_Online_Retail_Segmentation.docx  # academic report; opens with the Tetr cover page 1:1
│   └── Report_Online_Retail_Segmentation.pdf   # PDF export of the same report
├── figures/                                # all charts (PNG)
├── models/
│   ├── segmentation_pipeline.joblib        # log1p -> StandardScaler -> KMeans
│   └── cluster_personas.json               # cluster index -> persona name
├── data/
│   ├── customer_rfm_segments.csv           # per-customer RFM + segment (output)
│   └── online_retail_II.xlsx               # raw data (NOT in repo, see below)
├── docs/
│   ├── DATASET.md                          # source, access date, preprocessing
│   └── DEMO_VIDEO_SCRIPT.md                # timed walkthrough script
├── requirements.txt
└── README.md
```

## How to run

1. **Get the data.** Download `online_retail_II.xlsx` from the UCI Machine Learning Repository
   (https://doi.org/10.24432/C5CG6D) and place it in `data/`. It is not committed because of its size.
2. **Install dependencies** (Python 3.11+ recommended):
   ```bash
   pip install -r requirements.txt
   ```
3. **Run the notebook** top to bottom:
   ```bash
   jupyter notebook notebooks/online_retail_segmentation.ipynb
   ```
   The notebook reads `../data/online_retail_II.xlsx`, rebuilds every figure into `figures/`, writes the model to
   `models/`, and exports `data/customer_rfm_segments.csv`.

### Using the trained model on a new customer
```python
import joblib, json, pandas as pd
model = joblib.load("models/segmentation_pipeline.joblib")
personas = json.load(open("models/cluster_personas.json"))
new = pd.DataFrame([{"Recency": 12, "Frequency": 15, "Monetary": 6000.0}])  # days, orders, £
cluster = int(model.predict(new)[0])
print(cluster, "->", personas[str(cluster)])
```

## Method in brief
Clean at scale (documented waterfall) → RFM features on a fixed snapshot date → log-transform and standardize
(distance must not be dominated by Monetary) → choose k with the elbow method, confirmed by silhouette → K-Means
(k=4) → profile and name segments → translate into actions. Random seed is fixed (42) throughout for reproducibility.


## Data source and licence
*Online Retail II*, Daqing Chen, UCI Machine Learning Repository, 2012. Licence: CC BY 4.0.
https://doi.org/10.24432/C5CG6D . Accessed 15 October 2026. Full details in `docs/DATASET.md`.

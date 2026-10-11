# Customer Segmentation with RFM and Clustering

## Overview

An exploratory analysis of online retail customers using transaction history.
K-Means, Ward hierarchical clustering and DBSCAN are compared on the same
Recency, Frequency and Monetary (RFM) features. The selected groups are described
in the original units so their behavior can be interpreted.

The complete analysis, plots, measured tables and explanations are in
[customer_segmentation_clean.ipynb](customer_segmentation_clean.ipynb).
This is a notebook study, with no API or deployed prediction service.

## Analysis workflow

```text
Transactions -> cleaning -> customer RFM -> log1p -> standardization
             -> K-Means / Ward / DBSCAN -> metrics and customer profiles
             -> retrospective comparison of two time periods
```

- **Recency:** days since the customer's latest positive purchase.
- **Frequency:** number of distinct purchase invoices, not product rows.
- **Monetary:** gross positive purchase value in GBP. Returns are examined
  separately; they are not subtracted to calculate net revenue.
- `log1p` compresses the long spending/frequency tails without deleting valuable
  customers. Standardization gives the three features comparable scales.
- K-Means and Ward are compared for K=2 through K=6. DBSCAN uses an explicit
  parameter grid and reports both customer coverage and noise.

## Results and model choice

| Method | Recorded finding | Role |
|---|---|---|
| K-Means K=2 | Highest tested silhouette: **0.4385** | Broad two-group structure |
| K-Means K=4 | Smallest group about 20%; multi-start ARI **0.9931–1.0000** across tested seeds | Selected for detailed profiles |
| Ward | Supports the broad two-group structure; K=2 Davies-Bouldin **0.8191** | Complementary comparison |
| Selected DBSCAN | Two clusters, **198** noise customers, DBCV **−0.6263** | Density diagnostic |

K=4 is an interpretability choice, not a claim that it beats K=2's silhouette.
ARI checks agreement between assignments; it is not classification accuracy.
The K-Means stability check concerns initialization, not a new customer sample.

DBSCAN silhouette excludes noise customers, unlike the K-Means/Ward calculation.
Those scores therefore use different populations. The selected DBSCAN setting has
weak density separation; its noise group includes a sparse high-value customer
tail and should not automatically be discarded as invalid data.

## Final customer profiles

| Profile | Customer share | Median recency | Median frequency | Median gross purchase value | Gross value share |
|---|---:|---:|---:|---:|---:|
| High-Value Active | 20.48% | 17 days | 13 invoices | £4,965.48 | 74.00% |
| Established At Risk | 24.75% | 186 days | 4 invoices | £1,447.74 | 16.29% |
| Recent Developing | 21.45% | 25 days | 3 invoices | £729.25 | 6.15% |
| Inactive One-Time | 33.31% | 404.5 days | 1 invoice | £272.04 | 3.56% |

These names describe the observed profiles. They are not predefined classes,
permanent identities or verified churn predictions. The notebook assigns names
from profile values rather than assuming that numeric cluster IDs stay fixed.

## Temporal analysis

The source sheets overlap in December 2010. Two non-overlapping 365-day windows
avoid counting the overlap in both comparison periods:

- Period 1: **1 December 2009–30 November 2010**.
- Period 2: **1 December 2010–30 November 2011**.

The scaler and K-Means model are fitted on Period 1 and reused for Period 2.
However, the method and K=4 were chosen during earlier combined-data exploration.
This is a **retrospective consistency analysis**, not an untouched holdout.

Recorded results:

- Overall customer retention: **63.6%**.
- Returning customers' share of Period 2 gross purchase value: **82.1%**.
- High-value Loyal cohort retention: **95.8%**.
- One-time / Lapsed cohort retention: **38.4%**.

The period-specific names describe the separately fitted Period 1 profiles.
These relationships are descriptive; segment membership does not establish a
cause of retention, and an unobserved purchase is not proof of permanent churn.

## Project layout

```text
customer_segmentation_clean.ipynb   # Analysis, outputs and detailed explanations
requirements.txt                    # Recorded analysis dependencies
README.md
```

## Data setup

Download the official [UCI Online Retail II dataset](https://doi.org/10.24432/C5CG6D)
and place `online_retail_II.xlsx` in the repository root. Alternatively, use the
two CSV exports named `Year 2009-2010.csv` and `Year 2010-2011.csv`.
Only one input format is required; neither is included in Git.

The notebook checks the expected columns and overall date coverage. Rows without
a customer ID or valid date cannot enter customer-level profiles. Exact repeats
are removed, including rows shared by the source sheets. Removing within-file
repeats is an assumption because there is no unique line-item ID.

## Setup and usage

Python **3.13** was used for the saved notebook run. From the repository root:

**Windows PowerShell**

```powershell
py -3.13 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m jupyter lab
```

**macOS / Linux**

```bash
python3.13 -m venv .venv
.venv/bin/python -m pip install -r requirements.txt
.venv/bin/python -m jupyter lab
```

Open `customer_segmentation_clean.ipynb`, select the project's Python kernel,
restart it and run all cells from top to bottom. The saved outputs can also be
read directly on GitHub. DBCV and pairwise clustering diagnostics are the slowest
parts; running time and memory use depend on the machine.

## Limitations

- RFM excludes profit, product preference, demographics and marketing response.
- Missing customer IDs exclude 22.8% of raw rows from customer profiling.
- Monetary is gross purchase value, not net revenue or profit.
- Internal metrics and initialization checks do not validate a marketing action.
- K=4 is a profiling choice; another feature set or intended use may favor a
  different grouping. DBSCAN's weak result applies to the tested settings.
- The temporal comparison is retrospective and uses only two fixed windows.

Dataset: Chen, D. (2012), **Online Retail II**, UCI Machine Learning Repository.

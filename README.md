# Customer Segmentation with RFM and Clustering

This project compares three unsupervised clustering approaches for customer segmentation:

- K-Means
- Ward hierarchical clustering
- DBSCAN

The analysis uses Recency, Frequency, and Monetary (RFM) features built from two years of online retail transactions. The main goal is not only to find the highest clustering score, but also to understand which method produces customer profiles that can be explained and used.

The complete analysis, outputs, and interpretations are in [`customer_segmentation_clean.ipynb`](customer_segmentation_clean.ipynb).

## Main findings

- K-Means with `K=2` produced the strongest tested silhouette (`0.4385`).
- K-Means with `K=4` was selected for the final profiles because the groups were balanced, repeatable with the multi-start project setting, and easier to interpret in more detail.
- Ward clustering supported the broad two-group structure but produced weaker four-cluster separation.
- Within the tested DBSCAN grid, the selected practical setting produced two clusters and 198 noise customers. Its DBCV score was negative (`-0.6263`), indicating weak density separation for that setting.
- DBSCAN's noise group was not simply bad data: it contained a sparse, high-value customer tail.

### Final K-Means profiles

| Segment | Customer share | Median recency | Median frequency | Median gross purchase value | Gross value share |
|---|---:|---:|---:|---:|---:|
| High-Value Active | 20.48% | 17 days | 13 invoices | £4,965.48 | 74.00% |
| Established At Risk | 24.75% | 186 days | 4 invoices | £1,447.74 | 16.29% |
| Recent Developing | 21.45% | 25 days | 3 invoices | £729.25 | 6.15% |
| Inactive One-Time | 33.31% | 404.5 days | 1 invoice | £272.04 | 3.56% |

`Monetary` represents gross positive purchase value. Returns are described separately and are not subtracted to calculate net revenue.

## Time-based extension

The two source files overlap in December 2010. To avoid counting the same dates in both comparison periods, the notebook uses two non-overlapping 365-day windows:

- Period 1: 1 December 2009 to 30 November 2010
- Period 2: 1 December 2010 to 30 November 2011

A K-Means model and scaler are fitted only on Period 1, then reused to assign Period 2 customers. Period 2 values therefore do not change the fitted boundaries. However, the algorithm and `K=4` were chosen during the earlier combined-data exploration, so this is a retrospective temporal consistency analysis rather than a completely untouched holdout validation.

Key results:

- Overall customer retention: 63.6%
- Returning customers' share of Period 2 gross purchase value: 82.1%
- High-value Loyal segment retention: 95.8%
- One-time / Lapsed segment retention: 38.4%

These are descriptive relationships, not causal or predictive claims.

## Project structure

```text
.
|-- customer_segmentation_clean.ipynb   # Final analysis
|-- README.md
|-- requirements.txt
```

## Run locally

Python 3.13 was used for the final run.

```bash
python -m venv .venv
```

Activate the environment on Windows:

```powershell
.venv\Scripts\activate
```

Or on macOS/Linux:

```bash
source .venv/bin/activate
```

Then install the packages and start Jupyter:

```bash
pip install -r requirements.txt
jupyter lab
```

Open `customer_segmentation_clean.ipynb`, restart the kernel, and run all cells from top to bottom. The DBCV calculation is the slowest step and may take around a minute or more.

## Data setup

Download `online_retail_II.xlsx` from the [official UCI Online Retail II page](https://doi.org/10.24432/C5CG6D) and place it in the project root:

```text
online_retail_II.xlsx
```

The notebook also accepts two CSV exports with these exact names:

```text
Year 2009-2010.csv
Year 2010-2011.csv
```

Only one of these two input formats is needed. Raw data files are ignored by Git.

The notebook checks the expected columns and date coverage before the analysis continues. This helps catch a wrong file or renamed columns early.

## Metric notes

- **Silhouette:** higher is better; measures compactness and separation using distance.
- **Davies-Bouldin:** lower is better; compares within-cluster spread with separation.
- **ARI:** higher means two label assignments are more similar. In this project it is used for initialization stability and model agreement.
- **DBCV:** density-based validation used for DBSCAN; higher is better and negative values indicate weak density structure.

Scores from different populations are not treated as directly interchangeable. In particular, DBSCAN's non-noise silhouette excludes customers labelled as noise.

## Limitations

- Rows without a customer ID cannot be used in customer-level segmentation.
- Removing exact within-file repeats is an assumption because the source has no separate line-item identifier.
- Positive purchases are used for Monetary; the project does not calculate net revenue after returns.
- RFM does not include profit, product preference, demographics, or marketing response.
- Cluster names describe behavior during the observed period and are not permanent customer identities.
- `K=4` is an operational profiling choice; `K=2` remains the strongest metric-based split.

## Data source

Chen, D. (2012). *Online Retail II*. UCI Machine Learning Repository. [https://doi.org/10.24432/C5CG6D](https://doi.org/10.24432/C5CG6D)

# Customer Segmentation with RFM and Clustering

This project compares three unsupervised clustering approaches for customer segmentation:

*   **K-Means**
*   **Ward hierarchical clustering**
*   **DBSCAN**

The analysis uses Recency, Frequency, and Monetary (RFM) features built from two years of online retail transactions. The main goal is not only to find the highest clustering score, but also to understand which method produces customer profiles that can be explained and used.

The complete analysis, outputs, visual diagnostics, and interpretations are in [`customer_segmentation_clean.ipynb`](customer_segmentation_clean.ipynb).

---

## Analysis Pipeline

```text
Transactions
    ↓
Data Cleaning & Validation
    ↓
Customer-Level Aggregation
    ↓
RFM Feature Engineering
    ↓
Feature Scaling
    ↓
K-Means / Ward / DBSCAN
    ↓
Cluster Validation
    ↓
Customer Profiling
    ↓
Temporal Consistency Analysis
```

The project follows a complete unsupervised customer-segmentation workflow, starting from transaction-level data and ending with interpretable customer profiles and a retrospective temporal analysis.

---

## Main Findings

*   **K-Means with K=2** produced the strongest tested silhouette score (0.4385).
*   **K-Means with K=4** was selected for the final profiles because the groups were more balanced, repeatable under the project's multi-start setting, and easier to interpret in greater behavioral detail.
*   **Ward hierarchical clustering** supported the broad two-group structure but produced weaker four-cluster separation.
*   **DBSCAN:** Within the tested DBSCAN parameter grid, the selected practical configuration produced two clusters and 198 noise customers. Its DBCV score was negative (-0.6263), indicating weak density separation for that configuration.
*   **DBSCAN's noise group** was not simply bad data: it contained a sparse, high-value customer tail.
*   **Conclusion:** The final segmentation therefore prioritizes both quantitative clustering quality and practical interpretability rather than selecting a model from a single metric alone.

---

## Final K-Means Profiles

| Segment | Customer share | Median recency | Median frequency | Median gross purchase value | Gross value share |
|---|---|---|---|---|---|
| **High-Value Active** | 20.48% | 17 days | 13 invoices | £4,965.48 | 74.00% |
| **Established At Risk** | 24.75% | 186 days | 4 invoices | £1,447.74 | 16.29% |
| **Recent Developing** | 21.45% | 25 days | 3 invoices | £729.25 | 6.15% |
| **Inactive One-Time** | 33.31% | 404.5 days | 1 invoice | £272.04 | 3.56% |

> **Note:** Monetary represents gross positive purchase value. Returns are described separately and are not subtracted to calculate net revenue. These profiles are intended as behavioral descriptions of the observed customer population rather than permanent customer identities.

---

## Model Selection Decision Framework

The final K-Means configuration was selected using a combination of quantitative and practical criteria rather than relying on a single clustering metric.

| Criterion | K=2 | K=4 | Role in decision |
|---|---|---|---|
| **Silhouette** | Strongest tested | Lower than K=2 | Measures compactness and separation |
| **Cluster balance** | Broad two-group split | More balanced customer profiles | Avoids overly coarse segmentation |
| **Stability** | Stable | Repeatable under the project multi-start setting | Checks robustness |
| **Interpretability**| Broad behavioral split | More actionable customer profiles | Supports customer profiling |
| **Business usefulness**| Coarse segmentation | More detailed operational segmentation | Supports targeted analysis |

**Decision:** K=2 remains the strongest metric-based split, while K=4 was retained as the final operational profiling solution because it provides more balanced and interpretable behavioral groups. 

This distinction is intentional: the project does not claim that K=4 is mathematically superior to K=2. Instead, K=2 provides the strongest tested geometric separation according to silhouette, while K=4 provides a more useful level of behavioral granularity for customer profiling.

---

## Comparative Clustering Analysis

### K-Means
K-Means was evaluated across multiple values of K and used as the primary method for the final customer profiles. The analysis considers:
*   Cluster compactness and separation
*   Silhouette score
*   Cluster balance
*   Multi-start stability
*   Behavioral interpretability
*   Practical usefulness of the resulting customer profiles

*The project deliberately separates the best metric-based configuration from the final operational profiling configuration.*

### Ward Hierarchical Clustering
Ward's hierarchical clustering was used as a complementary method to evaluate whether the broad customer structure identified by K-Means was also present under a different clustering strategy.
*   The analysis showed that Ward supported the existence of a broad two-group structure, while the four-cluster configuration produced weaker separation.
*   This provides additional evidence that the broad segmentation pattern is not exclusively produced by one algorithm.

### DBSCAN
DBSCAN was included to test a density-based clustering perspective. Within the tested parameter grid, the selected practical configuration produced:
*   Two clusters
*   198 noise customers
*   Negative DBCV (-0.6263)

The negative DBCV value indicates weak density separation for the selected setting. Importantly, the customers labelled as DBSCAN noise were not interpreted as invalid observations. Instead, the group contained a sparse high-value customer tail, demonstrating why density-based outliers can sometimes represent meaningful business behavior rather than data quality problems.

---

## Visual Analysis

The notebook includes visual diagnostics for:
*   RFM feature distributions
*   K-Means cluster-selection analysis
*   Hierarchical clustering structure
*   DBSCAN density and noise behavior
*   Final segment profiles
*   Time-based retention and segment behavior

*These visual outputs are generated directly in `customer_segmentation_clean.ipynb`. The visual analysis is used together with numerical evaluation rather than as a replacement for quantitative validation.*

---

## RFM Feature Engineering

Customer-level features are created from transaction data using the standard RFM framework:

*   **Recency:** Measures how recently a customer made a purchase. Lower values indicate more recent activity.
*   **Frequency:** Measures how often a customer purchased, using the number of distinct invoices associated with the customer. Higher values indicate more repeated purchasing activity.
*   **Monetary:** Measures gross positive purchase value. Returns are not subtracted from Monetary, meaning the project deliberately avoids presenting gross purchase value as net revenue. This definition is kept consistent across the clustering and temporal analyses.

---

## Time-Based Extension

The two source files overlap in December 2010. To avoid counting the same dates in both comparison periods, the notebook uses two non-overlapping 365-day windows:
*   **Period 1:** 1 December 2009 to 30 November 2010
*   **Period 2:** 1 December 2010 to 30 November 2011

A K-Means model and scaler are fitted only on Period 1, then reused to assign Period 2 customers. Period 2 values therefore do not change the fitted feature-scaling boundaries or clustering model. 
*However, the algorithm and K=4 were chosen during the earlier combined-data exploration, so this should be interpreted as a retrospective temporal consistency analysis, not a completely untouched holdout validation.*

### Key Results
*   **Overall customer retention:** 63.6%
*   **Returning customers' share of Period 2 gross purchase value:** 82.1%
*   **High-value Loyal segment retention:** 95.8%
*   **One-time / Lapsed segment retention:** 38.4%

These results describe observed relationships across the two periods. They are descriptive rather than causal or predictive claims.

### Temporal Interpretation
The temporal analysis provides an additional behavioral perspective beyond static clustering. The strongest observed retention was associated with the high-value loyal customer group, while one-time or lapsed customers displayed substantially lower retention. 
This does not imply that segment membership causes future retention. Instead, it shows that the behavioral structure identified in the first period remains useful for describing customer activity in the subsequent period. The analysis is therefore best interpreted as a retrospective stability and consistency check.

---

## Metric Notes

The project uses multiple clustering validation measures because no single metric fully captures segmentation quality.

*   **Silhouette:** Higher values indicate better separation and compactness. Silhouette evaluates how similar each observation is to its own cluster compared with the nearest alternative cluster.
*   **Davies-Bouldin:** Lower values are better. The Davies-Bouldin index compares within-cluster spread with between-cluster separation.
*   **Adjusted Rand Index (ARI):** Higher values indicate greater similarity between two cluster assignments after correcting for chance agreement. In this project ARI is used for initialization stability and model agreement analysis.
*   **DBCV:** DBCV is a density-based validation metric used for DBSCAN. Higher values indicate stronger density-based clustering structure, while negative values indicate weak density separation.

### Cross-Metric Interpretation
Scores from different populations or clustering objectives are not treated as directly interchangeable. In particular:
*   DBSCAN may label observations as noise.
*   DBSCAN silhouette calculations may therefore exclude noise-labelled customers.
*   A strong K-Means silhouette does not automatically imply greater business usefulness.
*   A weaker numerical score can still correspond to a more interpretable customer segmentation.

For this reason, model selection combines metric-based evaluation with stability and interpretability.

---

## Cluster Stability and Interpretation

The project evaluates clustering behavior beyond a single model fit. The analysis considers:
*   Multiple K-Means initializations
*   Agreement across clustering approaches
*   Cluster balance
*   Segment interpretability
*   Reproducibility under the project's multi-start configuration

This helps distinguish a segmentation that is numerically plausible from one that is also stable enough to support interpretation. The goal is not to claim that the resulting clusters represent objectively true customer categories. Instead, they are data-driven behavioral groupings derived from the available RFM features.

---

## Project Structure

```text
.
|-- customer_segmentation_clean.ipynb   # Final analysis
|-- README.md
|-- requirements.txt
```
*The notebook contains the full preprocessing, feature engineering, model training, validation, visual analysis, customer profiling, and temporal analysis workflow.*

---

## Run Locally

Python 3.13 was used for the final run.

**1. Create a virtual environment:**
```bash
python -m venv .venv
```

**2. Activate the environment:**
*Windows:*
```bash
.venv\Scripts ctivate
```
*macOS/Linux:*
```bash
source .venv/bin/activate
```

**3. Install dependencies:**
```bash
pip install -r requirements.txt
```

**4. Start Jupyter and open the notebook:**
```bash
jupyter lab
```
Open `customer_segmentation_clean.ipynb`. Restart the kernel and run all cells from top to bottom. 
*(Note: The DBCV calculation is the slowest step and may take around a minute or more depending on the environment.)*

---

## Data Setup

Download `online_retail_II.xlsx` from the official UCI Online Retail II page: [https://doi.org/10.24432/C5CG6D](https://doi.org/10.24432/C5CG6D)

Place the file in the project root:
```text
online_retail_II.xlsx
```

The notebook also accepts two CSV exports with these exact names:
```text
Year 2009-2010.csv
Year 2010-2011.csv
```
*Only one of these two input formats is needed. Raw data files are ignored by Git. The notebook checks the expected columns and date coverage before the analysis continues. This helps catch incorrect files, renamed columns, or incomplete data early in the workflow.*

---

## Data Validation & Cleaning Considerations

### Data Validation
Before the clustering analysis begins, the notebook performs validation checks on the source data:
*   Expected column availability
*   Date coverage
*   Customer identifier availability
*   Transaction consistency
*   Numerical field handling
*   Input format validation

*The purpose is to fail early when the wrong input file or an incompatible schema is provided.*

### Data Cleaning Considerations
*   Customer-level segmentation requires a usable customer identifier. Rows without a Customer ID cannot be assigned reliably to a customer-level segment and are therefore excluded from customer-level aggregation.
*   The project also removes exact within-file repeats as a preprocessing assumption. Because the original source does not provide a separate line-item identifier suitable for distinguishing all possible duplicate records, this cleaning decision should be considered a modeling assumption rather than a universally correct data-cleaning rule.

---

## Business Interpretation

Although this is an unsupervised machine-learning project, the final output is intentionally expressed as interpretable customer behavior rather than as purely mathematical cluster IDs. 

### High-Value Active
*   Very recent activity
*   High purchase frequency
*   High gross purchase value
*   A disproportionately large share of total gross purchase value
*(This is the highest-value behavioral group in the observed dataset.)*

### Established At Risk
*   Older recent-purchase dates
*   Moderate purchase frequency
*   Meaningful historical spending
*(This group represents customers who have demonstrated established purchasing behavior but appear less recently active.)*

### Recent Developing
*   Recent activity
*   Lower purchase frequency
*   Lower monetary value than the high-value segment
*(This profile represents customers who have recently engaged but have not yet reached the purchasing depth of the strongest segment.)*

### Inactive One-Time
*   Very old recency
*   Single-invoice behavior
*   Low gross purchase value
*(This group represents the least active segment within the final K=4 profile.)*

> **Note:** These names are descriptive labels created for interpretation. They are not predefined classes and should not be treated as permanent customer identities.

---

## What the Project Demonstrates

This project focuses on more than simply running clustering algorithms. It demonstrates an end-to-end unsupervised machine-learning workflow involving:
*   Transaction-level data preparation
*   Customer-level aggregation
*   RFM feature engineering
*   Feature scaling
*   K-Means clustering
*   Hierarchical clustering
*   Density-based clustering
*   Cluster validation
*   Stability analysis
*   Business-oriented customer profiling
*   Temporal consistency analysis
*   Explicit discussion of modeling assumptions and limitations

The key methodological point is that **clustering quality is multi-dimensional.** A model can produce a stronger numerical separation while providing less useful business segmentation. Conversely, a slightly weaker numerical configuration may provide more interpretable and operationally useful customer profiles. The final decision therefore reflects both statistical and practical considerations.

---

## Limitations

*   Rows without a Customer ID cannot be used in customer-level segmentation.
*   Removing exact within-file repeats is an assumption because the source has no separate line-item identifier.
*   Positive purchases are used for Monetary; the project does not calculate net revenue after returns.
*   RFM does not include profit, product preference, demographics, or marketing response.
*   The segmentation is based only on behavioral transaction features available in the dataset.
*   Customer segment names describe behavior during the observed period and are not permanent customer identities.
*   K=4 is an operational profiling choice; K=2 remains the strongest metric-based split.
*   The temporal extension is retrospective rather than a completely untouched holdout validation because the clustering method and K=4 were selected during earlier combined-data exploration.
*   The analysis is descriptive and does not establish causal relationships between segment membership and future customer behavior.
*   The project does not claim that clustering labels represent objectively true or unique customer categories.
*   DBSCAN results are sensitive to density and parameter choices, and the selected configuration produced weak density separation according to DBCV.
*   Monetary is based on gross positive purchase value rather than net realized revenue.
*   No profitability, acquisition cost, marketing-response, demographic, or product-preference features are included.

---

## Reproducibility

The project is designed so that the analysis can be reproduced from the notebook using the documented input data formats and Python environment. The recommended execution order is:

1.  Create virtual environment
2.  Install requirements
3.  Add source dataset
4.  Open `customer_segmentation_clean.ipynb`
5.  Restart kernel
6.  Run all cells from top to bottom
7.  Review clustering diagnostics
8.  Review final customer profiles
9.  Review temporal consistency analysis

*The notebook contains the complete analysis workflow rather than relying on hidden preprocessing steps outside the repository.*

---

## Data Source

> Chen, D. (2012). Online Retail II. UCI Machine Learning Repository. [https://doi.org/10.24432/C5CG6D](https://doi.org/10.24432/C5CG6D)

---

## Final Takeaway

The project shows that effective customer segmentation is not simply a matter of maximizing one clustering metric. K-Means with K=2 produced the strongest tested silhouette score, indicating the clearest broad geometric separation in the evaluated feature space. However, K=4 was retained as the final operational profiling solution because it produced more balanced and interpretable behavioral groups. 

Ward clustering provided complementary evidence for the broad customer structure, while DBSCAN demonstrated that density-based methods can reveal sparse high-value customer behavior even when overall density separation is weak. The temporal extension further showed that the learned segmentation remained informative when the Period 1 model was reused on Period 2 customers.

Overall, the project emphasizes a practical machine-learning principle:
**The best clustering solution is not necessarily the one with the highest single metric, but the one whose statistical behavior, stability, interpretability, and intended use are all defensible.**

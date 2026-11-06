# Public Procurement: Competition, Clustering and Risk

Capstone project for the General Assembly Data Science course.

## Goal
1. **Predict competition:** how many **bids** a public tender in Croatia will receive, based on what is known before it is published (procedure type, contract type, sector, buyer, estimated value).
   Tenders with only one bid mean no real competition, a common red flag in public procurement.
2. **Find groups (clustering):** do tenders fall into natural groups, and do some groups look riskier (more delays, more manual reviews)?

## Data sources

### 1. Croatia: GTI Global Public Procurement Dataset (GPPD) 1/2
Government Transparency Institute: https://data.mendeley.com/datasets/fwzpywbhgw/3

- Contract-level public procurement data for 42 countries, 2006–2021, collected from official procurement portals and standardised to one format.
- Each record includes buyer and supplier, procedure type, CPV product code, prices and the number of bids received.
- This project uses the Croatia file: `HR_DIB_2023.csv.gz` (one row per bid).
- Licence: CC BY-NC 3.0.

> Fazekas, M., Tóth, B., Abdou, A., & Al-Shaibani, A. (2024). *Global Contract-level Public Procurement Dataset*. Data in Brief, 54, 110412.

The data file is not included in this repo because of its size (~700 MB unzipped). To run the EDA notebook, download `HR_DIB_2023.csv.gz` from the link above and put it in a `data/` folder.

### 2. Synthetic tender data (clustering notebook)
`synthetic_procurement_data.csv`: 2,500 tenders, 13 columns. It includes procurement method, category, agency type, supplier region, estimated value, number of bids and supplier history. It also has two outcome columns, `has_manual_review_flag` and `award_delayed`. There are no firm IDs.
Put the file next to the notebook to run it.

## Notebooks
| Notebook | What it does |
|---|---|
| `croatia_procurement_eda.ipynb` | Cleaning, one row per tender, EDA of bids by year, procedure, sector, price and buyer |
| `capstone_clustering_procurement.ipynb` | Clustering tenders (K-Means, Agglomerative, DBSCAN, PCA), risk screens per cluster, and classification of `award_delayed` |

## Clustering approach
The synthetic data has **no firm IDs**, so I cluster **tenders** by their features:

1. Scale 7 numeric features (log value, bids, supplier history, late-delivery rate, complexity, deadline, budget changes).
2. **K-Means**: pick k with the elbow and silhouette score.
3. Profile the clusters, then **screen** them on delays, manual reviews and procurement method. These columns are not used for clustering.
4. Check with **Agglomerative** (dendrogram) and **DBSCAN** (k-distance plot).
5. **PCA** to see the clusters in 2D.
6. **Classification**: predict `award_delayed` with Logistic Regression and Random Forest, with and without the cluster label.

## Findings so far
- **Weak clusters:** silhouette is about 0.11 for every k, and DBSCAN finds only one cluster. The tenders form one big cloud, not separate groups.
- **K-Means still gives 3 useful segments:** simple tenders, complex tenders, and tenders with many budget changes. The last two are delayed about 47–48% of the time vs 31% for simple tenders.
- **DBSCAN outliers** (unusual tenders) are delayed more often, so they are a good shortlist to check.
- **Classification works better** for the delay question: ROC-AUC about 0.70 (Logistic Regression). Contract value, supplier late-delivery rate, deadline and complexity matter most. Adding the cluster label does not help.
- These are **flags for further checking**, not proof of wrongdoing.

## Next steps
- Build a co-bidding network in Python (`networkx` + Louvain community detection) on the Croatian data, which has **bidder IDs per tender**. This finds groups of firms that often bid together.
- Baseline models for number of bids on the Croatian data: LinearRegression, then RandomForestRegressor, plus a single-bid classifier.

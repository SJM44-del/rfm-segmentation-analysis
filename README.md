# rfm-segmentation-analysis
RFM segmentation analysis on UCI Online Retail Dataset using Python, pandas, scikit-learn
RFM Customer Segmentation

Segmenting ~5,900 customers of a UK online retailer into groups using RFM (Recency, Frequency, Monetary) analysis and K-Means clustering, to answer a practical business question: where is the revenue concentrated, and which customers are worth retaining?

Overview
RFM is a long-established marketing technique that scores each customer on three behaviours:

Recency — how long since their last purchase (lower is better)
Frequency — how many separate orders they've placed
Monetary — how much they've spent in total

This project uses those three features from two years of raw transaction data

Dataset
Online Retail II — UCI Machine Learning Repository. Transactions from a UK-based online gift retailer, 1 Dec 2009 – 9 Dec 2011 (~1.07M rows before cleaning).

Source: https://archive.ics.uci.edu/dataset/502/online+retail+ii

The raw CSVs are not committed to this repo (size + good practice). To run the notebook, download online_retail_I.csv and online_retail_II.csv and place them in a data/ folder (or alongside the notebook, matching the paths in the loading cell).

Method
Load & combine the two annual files into one DataFrame.
Clean: parse dates with an explicit %d/%m/%Y %H:%M format; drop rows with no Customer ID then remove cancellations/returns and non-positive prices, drop exact duplicate rows, standardise column names.
Engineer RFM features per customer, recency against a fixed snapshot date (dataset max + 1 day), frequency as distinct invoices (not line items), monetary as summed line-item value.
Transform:log1p to tame heavy right-skew, then StandardScaler so all three features contribute equally to Euclidean distance.
Cluster: K-Means, with k chosen by the elbow method and silhouette score. Clusters are profiled and named from their R/F/M centroids.


Results

k = 4 was selected. The resulting segments:

| Segment | % of customers | % of revenue|	Avg recency (days) | Avg frequency| Avg monetary|
|----------|----------|----------||----------|----------|----------|----------|----------|----------|
| Champions     | 20.3%     | 73.9%           | 28 |                 |19.3|     |£10,731  |
| At Risk       | 24.8%     | 16.4%           | 230|                 | 5.1| 	  | £1,948  |
| New/Promising | 21.3%     | 6.2%            | 28 |                 | 3.1| 	   | £858    |
| Lost/One-time | 33.6%     | 3.6%            | 395|                 | 1.4| 	   |£317    |



Key findings
20.3% of customers (Champions) generate 73.9% of revenue.
At Risk customers still hold 16.4% of revenue despite no purchase in ~8 months, the highest-ROI group to target with retention.
A third of customers (33.6%) are one-time buyers contributing only 3.6% of revenue, pointing to a first-to-second-purchase conversion problem.

What each segment suggests doing
Champions: protect and reward: early access, loyalty perks, referrals.
At Risk:  win-back campaign; quantify the revenue at stake.
New / Promising:  toward a second purchase.
Lost / One-time: low-cost reactivation only; don't over-invest.

Limitations
~23% of rows had no customer ID and were dropped; the analysis covers identified customers only.
Monetary value is gross of returns: cancellations/returns were removed before aggregating, so refunded orders are not netted out. A small number of high-value customers whose large orders were later cancelled therefore appear more valuable than they were. (Netting returns is a planned refinement.)
Frequency is not tenure-adjusted,  a customer active for 2 years and one active for 2 months are compared on the same scale.
Snapshot-in-time analysis; it describes behaviour to date, it does not predict future churn.
Run it yourself
bash
git clone https://github.com/YOUR-USERNAME/rfm-customer-segmentation.git
cd rfm-customer-segmentation
pip install -r requirements.txt
# download the two CSVs into ./data/ (see Dataset above)
jupyter notebook rfm_segmentation.ipynb


Tech stack

Python · pandas · NumPy · scikit-learn (KMeans, StandardScaler) · Matplotlib · Jupyter

Possible extensions
Cross-validate clusters against classic RFM quintile scoring.
Tenure-adjust frequency.
Stability check across random seeds.
Hold out the final months to test whether "Champions" actually spend more later.

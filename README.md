# Beauty Products Supply Chain Analysis

Exploratory data analysis and visualization of a beauty-products supply chain dataset (100 SKUs across haircare, skincare and cosmetics). The project looks at customer preferences, supplier quality, logistics, and cost drivers, and uses a Decision Tree Regressor to rank the features that most influence product cost and manufacturing lead time.

## Contents

- [Dataset](#dataset)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Methodology](#methodology)
- [Visualizations](#visualizations)
- [Key Findings](#key-findings)
- [Limitations and Next Steps](#limitations-and-next-steps)

## Dataset

`supply_chain_data.csv` contains 100 rows and 24 columns with no missing values.

| Group | Columns |
|---|---|
| Product & sales | Product type, SKU, Price, Availability, Number of products sold, Revenue generated, Customer demographics |
| Inventory & ordering | Stock levels, Lead times, Order quantities |
| Shipping & logistics | Shipping times, Shipping carriers, Shipping costs, Transportation modes, Routes |
| Supplier & manufacturing | Supplier name, Location, Lead time, Production volumes, Manufacturing lead time, Manufacturing costs |
| Quality & cost | Inspection results, Defect rates, Costs |

Note: the dataset has both `Lead times` (ordering) and `Lead time` (supplier), which are distinct columns.

## Project Structure

```
.
├── Beauty_Products_Supply_Chain_Analysis.ipynb   # Full analysis
├── supply_chain_data.csv                         # Dataset
├── requirements.txt
└── README.md
```

## Getting Started

```bash
git clone https://github.com/mishra-krishna/Analysis-and-Optimization-of-Supply-Chain-Operations
cd Analysis-and-Optimization-of-Supply-Chain-Operations
pip install -r requirements.txt
jupyter notebook Beauty_Products_Supply_Chain_Analysis.ipynb
```

Core libraries: `pandas`, `numpy`, `matplotlib`, `scikit-learn`.

## Methodology

### 1. Data preparation
- Loaded the CSV with pandas; inspected with `head()`, `describe()`, `info()`.
- Confirmed there are no null values.
- One-hot encoded the categorical columns (product type, demographics, carriers, supplier, location, inspection results, transportation mode, route) for correlation and modelling.

### 2. Correlation analysis
- Computed Pearson (linear) and Spearman (monotonic) correlation matrices, filtering for |r| ≥ 0.5.
- The only pairs above the threshold were mutually exclusive dummy variables from the same categorical column (e.g. `Inspection results_Fail` vs `Inspection results_Pending`, `Routes_Route A` vs `Routes_Route B`). Among the original numeric features, no strong linear or monotonic relationships were found.

### 3. Feature importance
A `DecisionTreeRegressor` was fit on the encoded data to rank features for two targets, `Costs` and `Manufacturing lead time` (SKU excluded). A tree model was chosen because it can capture non-linear relationships that the correlation analysis would miss.

## Visualizations

| # | Chart | Question answered |
|---|---|---|
| 1 | Bar: order quantities by location | Which cities drive volume? |
| 2 | Pie: product type by customer demographic | Who buys what? |
| 3 | Bar: average defect rate by supplier | Which suppliers have the best/worst quality? |
| 4 | Stacked bar: order quantities by route and transport mode | How are orders distributed across logistics options? |
| 5 | Bar: average lead time by supplier and product type | Where are the slowest supply paths? |
| 6 | Box plot: manufacturing cost by product type | How much does production cost vary? |
| 7 | Box plot: total cost by product type | How much does total cost vary? |
| 8 | Scatter: shipping cost vs order quantity, per carrier | Do carriers price differently by volume? |
| 9 | Pie: revenue by customer demographic | Which segments generate the most revenue? |

## Key Findings

- **Customer preferences:** Female customers lean toward skincare and cosmetics; male customers toward haircare; non-binary customers toward skincare.
- **Supplier quality:** Supplier 5 has the highest average defect rate and Supplier 1 the lowest.
- **Logistics:** Order quantities vary across routes and transportation modes, shown in the stacked bar chart.
- **Lead time:** Average lead time differs noticeably by supplier and product type.
- **Cost drivers (Decision Tree):** The top features for `Costs` were manufacturing costs, lead time, stock levels, price, and sea transport. The top features for manufacturing lead time included shipping times, availability, manufacturing costs, and lead times.
- **No simple linear structure:** Pearson and Spearman analysis found no strong relationships between the original numeric features, so effects are likely non-linear or noisy.

## Limitations and Next Steps

- **Small sample (100 rows):** Feature importances from a single decision tree are unstable and may change between runs. Set a `random_state`, and consider a Random Forest or Gradient Boosting model with cross-validation.
- **SKU as a feature:** SKU is an identifier, not a meaningful predictor. Make sure it is excluded in every model (it appeared in the manufacturing lead time ranking in the current notebook and should be dropped there).
- **Correlation filtering:** The Spearman filter in the notebook reuses the Pearson matrix as its mask; recompute the mask from the Spearman matrix to be accurate.
- **Duplicated encoding:** `Location` is listed twice in the one-hot encoding list, producing duplicate columns.
- **Ideas:** Add model evaluation metrics (R², RMSE), supplier scorecards combining defect rate, lead time, and cost, and a what-if analysis of shifting volume between carriers or routes.

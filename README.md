# Vendor Performance Analysis

An end-to-end data analysis project that evaluates vendor and product performance for an inventory/retail business, using SQL, Python, and statistical hypothesis testing to identify pricing, profitability, and inventory optimization opportunities.

## 📌 Overview

This project analyzes vendor purchasing, sales, and pricing data stored in a SQLite database (`inventory.db`) to answer key business questions:

- Which vendors and brands drive the most sales and profit?
- Are there brands with high margins but low sales that need promotional attention?
- Does buying in bulk reduce unit purchase price, and by how much?
- Which vendors are sitting on excess, slow-moving (unsold) inventory?
- Is there a **statistically significant** difference in profit margins between top-performing and low-performing vendors?

The analysis combines SQL joins/aggregations across raw transactional tables, exploratory data analysis (distribution, outlier, and correlation checks), and inferential statistics (confidence intervals, two-sample t-test) to turn raw purchase/sales logs into vendor-level business insight.

## 🗂️ Data

Data is stored in a local SQLite database (`inventory.db`, excluded from version control via `.gitignore`) with the following raw tables:

| Table | Description | Records |
|---|---|---|
| `purchases` | Vendor-wise purchase transactions (brand, quantity, dollars) | ~2.37M |
| `purchase_prices` | Vendor/brand-wise actual purchase price and volume | ~12.3K |
| `sales` | Sales transactions (quantity sold, sales price, revenue, excise tax) | ~12.8M |
| `vendor_invoice` | Aggregated purchase orders per vendor, including freight cost | ~5.5K |
| `begin_inventory` / `end_inventory` | Opening/closing inventory snapshots | ~206K / ~224K |

From these, a consolidated analytical table — **`vendor_sales_summary`** (~10.7K rows) — is engineered by joining freight, purchase, and sales data per vendor and brand, then enriched with derived business metrics:

- `GrossProfit` = Total Sales Dollars − Total Purchase Dollars
- `ProfitMargin` = Gross Profit / Total Purchase Dollars × 100
- `StockTurnover` = Total Sales Quantity / Total Purchase Quantity
- `SalestoPurchaseRatio` = Total Sales Dollars / Total Purchase Dollars

## 🛠️ Tech Stack

- **Python** — pandas, numpy
- **SQL** — SQLite (via `sqlite3`), CTEs and multi-table joins for the summary query
- **Data Visualization** — matplotlib, seaborn (distribution plots, boxplots, correlation heatmaps)
- **Statistics** — scipy.stats (two-sample t-test, confidence intervals)
- **Jupyter Notebook** — analysis is organized into two notebooks (see below)

## 📁 Repository Structure

```
Vendor-Performance-Analysis/
│
├── exploratory data analysis.ipynb    # Table-level exploration + builds vendor_sales_summary table
├── vendor performance analysis.ipynb  # Core analysis: EDA, correlations, business questions, hypothesis test
├── ingestion_db.py                    # (placeholder) intended script to ingest raw CSVs into inventory.db
├── _get_vendor_summary.py             # (placeholder) intended script to generate vendor_sales_summary as a standalone module
├── .gitignore
└── README.md
```

> **Note:** `ingestion_db.py` and `_get_vendor_summary.py` are currently empty placeholder files — the corresponding logic for ingesting raw data and building the vendor summary table currently lives inline inside `exploratory data analysis.ipynb` (see the `create_vendor_summary()` function and the ingestion imports referenced there). Refactoring that logic into these two scripts as reusable, importable modules is a natural next step.

## 🔍 Key Findings

- **Scale:** 119 unique vendors, 8,544 unique brands, **$441.4M** in total sales dollars against **$307.3M** in total purchase dollars.
- **Vendor concentration:** The top 10 vendors account for **~65.7%** of total procurement spend — indicating significant vendor concentration risk.
- **Bulk pricing:** Vendors placing large orders pay **~72.4% less per unit** than those placing small orders ($10.78 vs. much higher unit cost), confirming strong bulk-purchase pricing leverage.
- **Idle capital:** Approximately **$2.7M** is locked in unsold inventory across vendors.
- **Profit margin vs. sales volume:** Low-performing vendors (bottom 25% by sales) have profit margins roughly **10 percentage points higher** than top-performing vendors (95% CI: ~40.5–42.6% vs. ~30.7–31.6%), suggesting a volume-margin tradeoff.
- **Hypothesis test:** A two-sample t-test (Welch's, unequal variance) comparing profit margins of top vs. low-performing vendors returned **t = -17.67, p < 0.0001** — the difference in profit margins is statistically significant, rejecting the null hypothesis.
- **Correlation insights:** Purchase price shows negligible correlation with sales revenue or gross profit; purchase and sales *quantity* are almost perfectly correlated (r = 0.999), confirming efficient inventory pass-through.

## ▶️ How to Run

1. Clone this repository.
2. Ensure `inventory.db` (SQLite database with the raw tables listed above) is present in the project root — this is not included in the repo (see `.gitignore`).
3. Install dependencies:
   ```bash
   pip install pandas numpy matplotlib seaborn scipy
   ```
4. Run the notebooks in order:
   - `exploratory data analysis.ipynb` — builds and writes the `vendor_sales_summary` table into `inventory.db`
   - `vendor performance analysis.ipynb` — loads `vendor_sales_summary` and runs the full analysis

## 📈 Possible Next Steps

- Move the ingestion and summary-table logic out of the notebook and into `ingestion_db.py` / `_get_vendor_summary.py` as callable functions, with logging (a `logs/` folder is already referenced for this).
- Build a Power BI / Tableau dashboard on top of `vendor_sales_summary` for ongoing monitoring.
- Extend hypothesis testing to brand-level and category-level comparisons.

---
*This README was generated based on the actual contents of the project's notebooks and data pipeline.*
# Vendor Performance Analysis

An end-to-end data analysis project that evaluates vendor and product performance for an inventory/retail business, using SQL, Python, and statistical hypothesis testing to identify pricing, profitability, and inventory optimization opportunities.

## 📌 Overview

This project analyzes vendor purchasing, sales, and pricing data stored in a SQLite database (`inventory.db`) to answer key business questions:

- Which vendors and brands drive the most sales and profit?
- Are there brands with high margins but low sales that need promotional attention?
- Does buying in bulk reduce unit purchase price, and by how much?
- Which vendors are sitting on excess, slow-moving (unsold) inventory?
- Is there a **statistically significant** difference in profit margins between top-performing and low-performing vendors?

The analysis combines SQL joins/aggregations across raw transactional tables, exploratory data analysis (distribution, outlier, and correlation checks), and inferential statistics (confidence intervals, two-sample t-test) to turn raw purchase/sales logs into vendor-level business insight.

## 🗂️ Data

Data is stored in a local SQLite database (`inventory.db`, excluded from version control via `.gitignore`) with the following raw tables:

| Table | Description | Records |
|---|---|---|
| `purchases` | Vendor-wise purchase transactions (brand, quantity, dollars) | ~2.37M |
| `purchase_prices` | Vendor/brand-wise actual purchase price and volume | ~12.3K |
| `sales` | Sales transactions (quantity sold, sales price, revenue, excise tax) | ~12.8M |
| `vendor_invoice` | Aggregated purchase orders per vendor, including freight cost | ~5.5K |
| `begin_inventory` / `end_inventory` | Opening/closing inventory snapshots | ~206K / ~224K |

From these, a consolidated analytical table — **`vendor_sales_summary`** (~10.7K rows) — is engineered by joining freight, purchase, and sales data per vendor and brand, then enriched with derived business metrics:

- `GrossProfit` = Total Sales Dollars − Total Purchase Dollars
- `ProfitMargin` = Gross Profit / Total Purchase Dollars × 100
- `StockTurnover` = Total Sales Quantity / Total Purchase Quantity
- `SalestoPurchaseRatio` = Total Sales Dollars / Total Purchase Dollars

## 🛠️ Tech Stack

- **Python** — pandas, numpy
- **SQL** — SQLite (via `sqlite3`), CTEs and multi-table joins for the summary query
- **Data Visualization** — matplotlib, seaborn (distribution plots, boxplots, correlation heatmaps)
- **Statistics** — scipy.stats (two-sample t-test, confidence intervals)
- **Jupyter Notebook** — analysis is organized into two notebooks (see below)

## 📁 Repository Structure

```
Vendor-Performance-Analysis/
│
├── exploratory data analysis.ipynb    # Table-level exploration + builds vendor_sales_summary table
├── vendor performance analysis.ipynb  # Core analysis: EDA, correlations, business questions, hypothesis test
├── ingestion_db.py                    # (placeholder) intended script to ingest raw CSVs into inventory.db
├── _get_vendor_summary.py             # (placeholder) intended script to generate vendor_sales_summary as a standalone module
├── .gitignore
└── README.md
```

> **Note:** `ingestion_db.py` and `_get_vendor_summary.py` are currently empty placeholder files — the corresponding logic for ingesting raw data and building the vendor summary table currently lives inline inside `exploratory data analysis.ipynb` (see the `create_vendor_summary()` function and the ingestion imports referenced there). Refactoring that logic into these two scripts as reusable, importable modules is a natural next step.

## 🔍 Key Findings

- **Scale:** 119 unique vendors, 8,544 unique brands, **$441.4M** in total sales dollars against **$307.3M** in total purchase dollars.
- **Vendor concentration:** The top 10 vendors account for **~65.7%** of total procurement spend — indicating significant vendor concentration risk.
- **Bulk pricing:** Vendors placing large orders pay **~72.4% less per unit** than those placing small orders ($10.78 vs. much higher unit cost), confirming strong bulk-purchase pricing leverage.
- **Idle capital:** Approximately **$2.7M** is locked in unsold inventory across vendors.
- **Profit margin vs. sales volume:** Low-performing vendors (bottom 25% by sales) have profit margins roughly **10 percentage points higher** than top-performing vendors (95% CI: ~40.5–42.6% vs. ~30.7–31.6%), suggesting a volume-margin tradeoff.
- **Hypothesis test:** A two-sample t-test (Welch's, unequal variance) comparing profit margins of top vs. low-performing vendors returned **t = -17.67, p < 0.0001** — the difference in profit margins is statistically significant, rejecting the null hypothesis.
- **Correlation insights:** Purchase price shows negligible correlation with sales revenue or gross profit; purchase and sales *quantity* are almost perfectly correlated (r = 0.999), confirming efficient inventory pass-through.

## ▶️ How to Run

1. Clone this repository.
2. Ensure `inventory.db` (SQLite database with the raw tables listed above) is present in the project root — this is not included in the repo (see `.gitignore`).
3. Install dependencies:
   ```bash
   pip install pandas numpy matplotlib seaborn scipy
   ```
4. Run the notebooks in order:
   - `exploratory data analysis.ipynb` — builds and writes the `vendor_sales_summary` table into `inventory.db`
   - `vendor performance analysis.ipynb` — loads `vendor_sales_summary` and runs the full analysis

## 📈 Possible Next Steps

- Move the ingestion and summary-table logic out of the notebook and into `ingestion_db.py` / `_get_vendor_summary.py` as callable functions, with logging (a `logs/` folder is already referenced for this).
- Build a Power BI / Tableau dashboard on top of `vendor_sales_summary` for ongoing monitoring.
- Extend hypothesis testing to brand-level and category-level comparisons.

---
*This README was generated based on the actual contents of the project's notebooks and data pipeline.*

# Superstore Sales & Profitability Analysis

An end-to-end retail analytics project demonstrating how to transform transactional data into business insights using **Python, SQL, SQLite, data modeling, and Power BI-ready outputs**.

The analysis focuses on a practical business question:

> **Which products, customers, discounts, and operational patterns are associated with stronger or weaker profitability?**

[**View the interactive dashboard**](https://shubham-k-jha.github.io/superstore-analysis/) · [Repository](https://github.com/shubham-k-jha/superstore-analysis)

---

## Project at a glance

- **Domain:** Retail / Sales / Business Intelligence
- **Dataset:** Historical Superstore transactions
- **Period in the current dataset:** 2009–2012
- **Source records:** 8,399 transaction line items across 5,496 orders
- **Analysis tools:** Python, Pandas, NumPy, Matplotlib, SQL, SQLite
- **BI output:** Star-schema CSV tables prepared for Power BI
- **Web output:** Static interactive dashboard hosted with GitHub Pages

**Currency note:** Monetary figures are presented in the source dataset's units. Verify the original dataset's currency denomination before interpreting them as CAD or another specific currency.

## Business questions

This project investigates:

1. How do sales and profit change over time?
2. Which product sub-categories generate profit or losses?
3. How does discounting relate to profit margin?
4. How do shipping time and order priority compare?
5. How does profitability vary by region or province?
6. Which customers contribute the most recorded profit?
7. What data-quality checks are needed before reporting these results?

The results are descriptive. Observed associations—such as discounts coinciding with lower margins—do not, by themselves, establish causation.

## Key findings

The current analysis outputs report the following patterns. Re-run the pipeline to reproduce the figures before using them in a formal report or decision.

- **Loss-making line items:** 4,264 of 8,399 line items (about 50.8%) have negative recorded profit.
- **Sub-category differences:** Tables and Bookcases show substantial recorded losses, while Telephones, Office Machines, and Binders show comparatively high recorded profit.
- **Discount and margin:** The current analysis reports negative weighted margins for higher-discount bands. This is a descriptive relationship, not proof that discounts alone caused the losses.
- **Shipping priority:** Low-priority orders have a longer average shipping time in the current analysis than most other priority groups.
- **Regional variation:** Recorded profit varies across provinces. Shipping cost, order volume, product mix, and other factors should be examined before attributing these differences to geography.

Figures above depend on the supplied dataset and the current pipeline. They are not independently audited financial results.

## Data source and quality

The project uses the historical Superstore CSV made available by the [Curran data repository](https://raw.githubusercontent.com/curran/data/gh-pages/superstoreSales/superstoreSales.csv).

The source export contains 21 columns, including order and shipping dates, sales, profit, discount, shipping cost, product margin, customer, product, and geographic fields.

Quality checks and transformations include:

- Parsing order and shipping dates
- Removing rows whose dates cannot be parsed
- Rejecting records where the shipping date precedes the order date
- Imputing missing product-base-margin values with the median for the corresponding product category
- Creating consistent dimension tables and validating fact-table joins

The category-median imputation is an explicit modeling choice. It does not change the recorded sales or profit fields, but it does affect the imputed margin field and should be documented when interpreting margin-related analyses.

## Workflow

```text
Raw CSV
  |
  v
Input validation and date handling
  |
  v
Cleaned transaction table
  |
  v
Star-schema modeling
  |
  +----------------------+
  |                      |
  v                      v
SQLite + SQL analysis    Power BI-ready CSV tables
  |
  v
CSV results and charts
  |
  v
Interactive HTML dashboard
```

### 1. Clean and model the data

`scripts/clean_and_model.py`

- Reads the source CSV
- Validates required columns and data availability
- Parses dates and derives shipping duration and profit margin
- Builds `fact_sales`, `dim_customer`, `dim_product`, `dim_region`, and `dim_date`
- Exports the cleaned table and Power BI-ready dimensions/fact table

### 2. Run SQL analysis and generate charts

`scripts/run_sql_analysis.py`

- Loads the star-schema CSVs into SQLite
- Executes the eight queries defined in `sql/01_analysis_queries.sql`
- Saves query outputs under `data/results/`
- Generates analytical figures under `visuals/`

### 3. Explore the dashboard

Open the root-level `index.html` locally, or use the [published dashboard](https://shubham-k-jha.github.io/superstore-analysis/).

The dashboard is a static HTML/JavaScript artifact. Its embedded metrics should be checked against regenerated pipeline outputs whenever the analysis changes.

## Visuals

| Profit by sub-category | Discount vs. weighted margin |
|---|---|
| ![Profit by sub-category](visuals/profit_by_subcategory.png) | ![Discount vs weighted margin](visuals/discount_vs_margin.png) |

| Monthly sales and profit | Shipping days by mode |
|---|---|
| ![Monthly sales and profit](visuals/monthly_sales_profit_trend.png) | ![Shipping days by mode](visuals/shipping_days_by_mode.png) |

![Losses by category](visuals/losses_by_category.png)

## Power BI data model

The pipeline exports five CSV tables to `data/powerbi/`:

- `fact_sales.csv`
- `dim_customer.csv`
- `dim_product.csv`
- `dim_region.csv`
- `dim_date.csv`

The intended model is a central sales fact table connected to customer, product, region, and date dimensions. Import the CSVs into Power BI and validate relationship cardinality and filter direction before building measures.

A Power BI build guide is available at `powerbi_guide/POWERBI_GUIDE.md`.

## Repository structure

```text
superstore-analysis/
├── data/
│   ├── raw/              # Place the source CSV here
│   ├── clean/            # Generated cleaned transaction table
│   ├── powerbi/          # Generated star-schema CSV tables
│   └── results/          # Generated SQL query results
├── dashboard/
│   └── index.html        # Dashboard copy
├── index.html            # GitHub Pages entry point
├── powerbi_guide/
│   └── POWERBI_GUIDE.md
├── scripts/
│   ├── clean_and_model.py
│   └── run_sql_analysis.py
├── sql/
│   └── 01_analysis_queries.sql
├── visuals/              # Generated charts and dashboard image
├── requirements.txt
└── README.md
```

Generated files may be ignored by Git, depending on the repository's ignore rules. The raw source CSV is expected at `data/raw/superstore_sales.csv`.

## Run locally

### Requirements

- Python 3.10 or newer recommended
- pip
- Git

### 1. Clone the repository

```bash
git clone https://github.com/shubham-k-jha/superstore-analysis.git
cd superstore-analysis
```

### 2. Create a virtual environment

Linux/macOS:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Windows PowerShell:

```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
```

### 3. Install dependencies

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

### 4. Add the source dataset

Download the [Superstore CSV](https://raw.githubusercontent.com/curran/data/gh-pages/superstoreSales/superstoreSales.csv) and save it as:

```text
data/raw/superstore_sales.csv
```

### 5. Build the cleaned data and star schema

```bash
python scripts/clean_and_model.py
```

### 6. Run the SQL analysis and charts

```bash
python scripts/run_sql_analysis.py
```

The scripts write generated tables, query results, a SQLite database, and chart images to the project directories described above.

### 7. Open the dashboard

Open `index.html` in your browser, or visit the [live dashboard](https://shubham-k-jha.github.io/superstore-analysis/).

## SQL and analytics concepts demonstrated

- Multi-table joins and star-schema querying
- Aggregation with `GROUP BY` and `HAVING`
- Common table expressions (CTEs) and subqueries
- Window functions, including ranking and bucketing
- Date-based trend analysis
- Customer and product profitability analysis
- Weighted profit-margin calculations
- Data-quality validation and dimensional modeling

## Limitations and responsible interpretation

- This is a historical dataset; findings do not describe current retail conditions.
- Monetary denomination should be confirmed from the original source before assigning a currency symbol.
- Negative profit on a transaction line is not necessarily equivalent to a loss-making order; aggregate at the correct business grain for each question.
- Correlation between discount level and margin is not causal evidence.
- The HTML dashboard contains embedded figures and must be kept consistent with newly generated outputs.
- The pipeline should be run and its outputs checked in a clean environment before treating the figures as reproducible.

## Skills demonstrated

**Analytics:** KPI definition, profitability analysis, customer/product analysis, regional comparisons, and translating results into business questions.

**SQL:** Joins, aggregations, CTEs, subqueries, window functions, and SQLite.

**Python:** Pandas, NumPy, data validation, transformation, reproducible scripts, and Matplotlib.

**BI and reporting:** Star-schema design, Power BI-ready data modeling, dashboard communication, and analytical documentation.

## Author

**Shubham Jha**  
Data Analytics | Business Intelligence | Python | SQL | Power BI

---

If you find the project useful, explore the SQL queries, reproduce the pipeline, and compare the generated results with the dashboard.

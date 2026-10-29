# Customer Churn Analysis

A data analysis project in a Jupyter Notebook that explores customer churn for a subscription business. It loads data from a SQLite database, cleans it, builds churn-related features and KPIs, and visualizes the results with Matplotlib and Seaborn.

## Features

- **SQL data loading**: reads every table from a SQLite database into its own pandas DataFrame automatically.
- **Data cleaning**: renames columns, drops unused ones, fixes data types, standardizes values, and fills missing values.
- **Feature engineering**: creates a churn flag, complaint counts, tenure, customer age, and a churn risk category.
- **KPI calculation**: churn rate, retention rate, ARPU, average tenure, revenue at risk, escalation rate, and more.
- **Visualization**: line chart, bar charts, correlation heatmaps, pairplot, and category plots.
- **Export**: saves the merged, cleaned dataset to `exported_churn_data.csv`.

## Requirements

- Python 3.8 or higher
- Jupyter Notebook or JupyterLab

Libraries:

```bash
pip install numpy pandas matplotlib seaborn jupyter
```

`sqlite3` is part of the Python standard library, so it needs no installation.

## How to Run

1. Place `customer_churn.db` in the same folder as the notebook.
2. Open a terminal in that folder.
3. Start Jupyter:

```bash
jupyter notebook churn_analysis.ipynb
```

4. Run the cells from top to bottom (**Kernel → Restart & Run All**).

> **Note:** the notebook expects a SQLite database named `customer_churn.db`. It is not included in this repository, so add your own copy with the same tables and columns.

## Dataset

The database contains three tables:

| Table             | Columns                                                                                                                                                  |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `db_customer`     | `customerid`, `name`, `country`, `state`, `gender`, `dob`, `interests`, `pincode`                                                                        |
| `db_subscription` | `customerid`, `subscription_start_date`, `subscription_type`, `renewal_date`, `plan_type`, `contract_type`, `cancellation_date`, `cancellation_reason`, `monthly_charges`, `cltv`, `churn_score` |
| `db_support`      | `customerid`, `complaint_date`, `escalations`, `csat_score`, `col_1`, `comment`                                                                          |

## Notebook Walkthrough

### 1. Import Data

Connects to `customer_churn.db`, finds all tables via `sqlite_master`, and creates `df_db_customer`, `df_db_subscription`, and `df_db_support`. It also prints each table's column names using `PRAGMA table_info`.

### 2. Data Cleaning

| Table          | Cleaning step                                                                                     |
|----------------|---------------------------------------------------------------------------------------------------|
| `customer`     | Rename `name` → `customer_name`                                                                   |
| `customer`     | Drop `interests` and `pincode`                                                                    |
| `customer`     | Convert `dob` to datetime                                                                         |
| `customer`     | Standardize `gender` (`Men` → `Male`, `Women` → `Female`)                                         |
| `customer`     | Fill missing `country` values using a state → country mapping                                     |
| `subscription` | Convert `subscription_start_date`, `renewal_date`, `cancellation_date` to datetime               |
| `support`      | Convert `complaint_date` to datetime                                                              |
| `support`      | Drop `col_1` and `comment`                                                                        |

### 3. Feature Engineering and Analysis

- **Churn flag**: `1` if a cancellation date exists, otherwise `0`.
- **Duplicate handling**: some customers have multiple complaints, which caused extra rows when merging. The notebook adds a `complaint_count` column and keeps only the latest complaint per customer.
- **Merge**: subscription, customer, and support data are joined on `customerid` and exported to `exported_churn_data.csv`.
- **Churn risk**: based on `churn_score`.

| churn_score | churn_risk |
|-------------|------------|
| below 50    | low        |
| 50 to 69    | med        |
| 70 and above| high       |

### KPIs Calculated

| KPI                           | Description                                                        |
|-------------------------------|--------------------------------------------------------------------|
| Churn rate                    | Percentage of customers who cancelled                              |
| Retention rate                | 100 minus churn rate                                               |
| Churn by plan / state / type  | Churn rate grouped by `plan_type`, `state`, `subscription_type`    |
| ARPU                          | Average monthly revenue per user                                   |
| Customer age                  | Age in years calculated from `dob`                                 |
| Average tenure                | Days from start to cancellation (or to today if still active)      |
| Revenue at risk               | Total monthly charges of churned customers                         |
| Escalation rate               | Percentage of support cases that were escalated                    |
| Average complaints per user   | Total complaints divided by unique customers                       |
| Escalation vs churn           | Correlation between escalations and churn                          |

### 4. Visualization

**Matplotlib**
- Monthly churn trend (line chart)
- Churn rate by plan type (bar chart)
- Churn rate by state (bar chart)

**Seaborn**
- Correlation heatmap, first with plain category codes and then with ordered encoding (`Basic < Standard < Premium`, `Monthly < Annual`, `low < med < high`)
- Pairplot of encoded features
- Catplot of monthly charges by plan type, split by gender and churn risk

**Pivot tables**
- Churn rate by plan type
- Total revenue, unique customers, and churn rate by plan type

## Output Files

| File                        | Description                                  |
|-----------------------------|----------------------------------------------|
| `exported_churn_data.csv`   | Merged and cleaned dataset used for analysis |

## Project Structure

```
.
├── churn_analysis.ipynb      # Main analysis notebook
├── customer_churn.db         # SQLite database (add your own)
├── exported_churn_data.csv   # Generated by the notebook
└── README.md                 # Project documentation
```

## Possible Improvements

- Add `()` to `plt.show` in the monthly churn trend cell so it is actually called
- Use `.copy()` when creating `df_encoded` to avoid pandas `SettingWithCopyWarning`
- Add titles and axis labels to the plot cells that are missing them
- Complete the pending "homework" items: churn by state and subscription type together with revenue and user counts
- Build a predictive churn model (e.g. logistic regression or random forest)
- Fix typos in variable names and labels (`chure_rate`, `retenion_rate`, `esclation_rate`)

## License

This project is free to use and modify for learning purposes.

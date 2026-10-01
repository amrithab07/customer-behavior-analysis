# Customer Shopping Behavior Analysis

An end-to-end data analytics project that cleans and engineers features from a retail customer dataset in **Python**, answers business questions with **PostgreSQL**, and presents the findings in an interactive **Power BI** dashboard.

---

## Project Overview

Retailers collect a lot of data about how customers shop, but it only becomes useful once it is cleaned, queried and visualised. This project analyses **3,900 customer transactions** to understand:

- Who the customers are (age, gender, location, subscription status)
- What they buy and how much they spend
- How discounts, shipping type and subscriptions relate to spending
- Which customers are new, returning or loyal

**Workflow:** `CSV → Pandas (clean + feature engineering) → PostgreSQL (business queries) → Power BI (dashboard)`

---

## Repository Structure

```
├── customer_shopping_behavior_analysis.ipynb   # Data cleaning & feature engineering, loads data into PostgreSQL
├── customer_shopping_behavior_sql.sql          # 10 business-question SQL queries
├── customer_behavior_dashboard.pbix            # Power BI dashboard
├── customer_shopping_behavior.csv              # Raw dataset (add this if you're allowed to share it)
└── README.md
```

---

## Dataset

3,900 rows × 18 columns, one row per customer purchase.

| Column | Description |
|---|---|
| Customer ID | Unique customer identifier |
| Age, Gender | Customer demographics |
| Item Purchased, Category | Product bought (25 items across 4 categories) |
| Purchase Amount (USD) | Transaction value (range $20 – $100) |
| Location | US state (50 states) |
| Size, Color, Season | Product attributes |
| Review Rating | Rating given (2.5 – 5.0) |
| Subscription Status | Whether the customer is subscribed |
| Shipping Type | Standard, Express, Free Shipping, Next Day Air, etc. |
| Discount Applied, Promo Code Used | Discount information |
| Previous Purchases | Number of earlier purchases |
| Payment Method | Venmo, PayPal, Credit Card, Cash, etc. |
| Frequency of Purchases | Weekly, Fortnightly, Monthly, Quarterly, Annually, etc. |

---

## Data Preparation (Python)

Done in `customer_shopping_behavior_analysis.ipynb`:

1. **Loaded & inspected** the data with `head()`, `info()` and `describe()`.
2. **Handled missing values:** 37 missing `Review Rating` values were filled with the **median rating of the same product category**.
3. **Standardised column names** to `snake_case` (e.g. `Purchase Amount (USD)` → `purchase_amount`).
4. **Feature engineering**
   - `age_group`: customers split into four quartile-based groups (Young Adult, Adult, Middle-aged, Senior) using `pd.qcut`.
   - `purchase_frequency_days`: purchase frequency text converted to a number of days (e.g. Weekly → 7, Monthly → 30, Annually → 365).
5. **Removed redundancy:** `promo_code_used` was identical to `discount_applied` in every row, so it was dropped.
6. **Loaded the cleaned data into PostgreSQL** (table `customer`, database `customer_behavior`) using SQLAlchemy and psycopg2.

---

## Business Questions Answered (SQL)

All queries are in `customer_shopping_behavior_sql.sql`.

| # | Question | SQL concepts used |
|---|---|---|
| 1 | Total revenue from male vs. female customers | `GROUP BY`, `SUM` |
| 2 | Customers who used a discount but still spent above the average purchase amount | Subquery |
| 3 | Top 5 products by average review rating | `AVG`, `ORDER BY`, `LIMIT` |
| 4 | Average purchase amount: Standard vs. Express shipping | `AVG`, filtering |
| 5 | Do subscribers spend more? Compare average spend and total revenue | Aggregations |
| 6 | Top 5 products with the highest share of discounted purchases | `CASE WHEN` |
| 7 | Segment customers into New / Returning / Loyal | CTE, `CASE WHEN` |
| 8 | Top 3 most purchased products in each category | CTE, `ROW_NUMBER() OVER (PARTITION BY ...)` |
| 9 | Are repeat buyers (>5 previous purchases) likely to subscribe? | Filtering, `GROUP BY` |
| 10 | Revenue contribution of each age group | `GROUP BY`, `ORDER BY` |

**Customer segments (Q7):** New = 1 previous purchase, Returning = 2–10, Loyal = more than 10.

---

## Power BI Dashboard

`customer_behavior_dashboard.pbix` is a single-page interactive dashboard connected to the PostgreSQL `customer` table.

**KPI cards:** Number of Customers · Average Purchase Amount · Average Review Rating

**Visuals**
- Sales by Age Group
- Revenue by Age Group
- Sales by Category
- Revenue by Category
- % of Customers by Subscription Status (donut chart)

**Interactive filters:** Gender · Category · Subscription Status · Shipping Type

<!-- Add a screenshot of the dashboard here, e.g. ![Dashboard](images/dashboard.png) -->

---

## Tech Stack

- **Python:** pandas, SQLAlchemy, psycopg2
- **Database:** PostgreSQL
- **BI tool:** Power BI
- **Environment:** Jupyter Notebook

---

## How to Run

1. **Clone the repo**
   ```bash
   git clone https://github.com/<your-username>/<your-repo>.git
   cd <your-repo>
   ```
2. **Install dependencies**
   ```bash
   pip install pandas sqlalchemy psycopg2-binary jupyter
   ```
3. **Create a PostgreSQL database** named `customer_behavior`.
4. **Set your database credentials** as environment variables (do not hard-code them):
   ```bash
   export PG_USER=postgres
   export PG_PASSWORD=your_password
   ```
   and read them in the notebook with `os.environ["PG_PASSWORD"]`.
5. **Run the notebook** `customer_shopping_behavior_analysis.ipynb` to clean the data and load it into the `customer` table.
6. **Run the queries** in `customer_shopping_behavior_sql.sql` using pgAdmin, DBeaver or `psql`.
7. **Open the dashboard** `customer_behavior_dashboard.pbix` in Power BI Desktop and point the data source to your local PostgreSQL database.

---

## Future Improvements

- Add RFM (Recency, Frequency, Monetary) customer segmentation
- Build a churn / repeat-purchase prediction model
- Add a state-level map visual for location analysis
- Automate the pipeline with a scheduled script

---

## Author

**Baratam Amritha**
B.Tech Computer Science Engineering, VIT Chennai

- GitHub: [@amrithab07](https://github.com/amrithab07)
- LinkedIn: [Amritha Baratam](https://www.linkedin.com/in/amrithabaratam)

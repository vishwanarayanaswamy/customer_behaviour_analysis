# 🛍️ Customer Shopping Behavior Analysis

End-to-end analysis of retail customer behaviour using **Python (pandas)**, **PostgreSQL** and **Power BI**: from raw CSV to a cleaned, feature-engineered database table, SQL answers to business questions, and an interactive dashboard.

---

## 📌 Overview

The project turns 3,900 customer transaction records into clear, business-focused insights about revenue, customer segments, products, shipping, subscriptions and payment behaviour.

**Workflow**

```
Raw CSV  →  Python / pandas  →  Cleaning & feature engineering  →  PostgreSQL  →  SQL analysis  →  Power BI dashboard
```

---

## 🗂 Repository Contents

| File | Description |
|---|---|
| `customer_shopping_behavior.csv` | Raw dataset (3,900 rows × 18 columns) |
| `customer_data_exploration.ipynb` | EDA, cleaning, feature engineering, load into PostgreSQL |
| `customer_behaviour.sql` | 7 SQL queries answering business questions |
| `customer behaviour.pbix` | Interactive Power BI dashboard |

---

## 📊 Dataset

**3,900 customers · 18 columns**

| Group | Columns |
|---|---|
| Customer | `Customer ID`, `Age`, `Gender`, `Location`, `Subscription Status`, `Previous Purchases`, `Frequency of Purchases` |
| Purchase | `Item Purchased`, `Category`, `Purchase Amount (USD)`, `Size`, `Color`, `Season` |
| Experience & payment | `Review Rating`, `Shipping Type`, `Discount Applied`, `Promo Code Used`, `Payment Method` |

Categories: Clothing, Accessories, Footwear, Outerwear.

---

## 🧹 Data Preparation (Python)

1. Loaded the CSV and inspected structure with `info()`, `describe()` and `dtypes`.
2. Found **37 missing values in `Review Rating`** → filled with the **median rating of each product category**.
3. Standardised column names (lower-case, underscores) and renamed `purchase_amount_(usd)` → `purchase_amount`.
4. **Feature engineering**
   - `age_group` – quartile-based bins via `pd.qcut`: `young_adult` (18–31), `adult` (32–44), `middle-aged` (45–57), `senior` (58–70).
   - `purchase_frequency_rate` – purchase frequency converted to approximate days (Weekly = 7, Fortnightly/Bi-Weekly = 14, Monthly = 30, Quarterly/Every 3 Months = 90, Annually = 365).
5. Verified that `Discount Applied` and `Promo Code Used` are identical for every row, then **dropped `promo_code_used`** as redundant.
6. Loaded the cleaned DataFrame into a PostgreSQL table named `customer` using SQLAlchemy + psycopg2.

---

## 🗄 SQL Analysis

All queries are in [`customer_behaviour.sql`](customer_behaviour.sql). Results below were verified by reproducing each query on the cleaned data.

| # | Business question | Result |
|---|---|---|
| 1 | Total revenue by gender | Male **$157,890** vs Female **$75,191** (total $233,081) |
| 2 | Discounted customers who spent at or above the average purchase ($59.76) | **839** of the 1,677 customers who used a discount |
| 3 | Top 5 products by average review rating | Gloves (3.86), Sandals (3.84), Boots (3.82), Hat (3.80), Skirt (3.78) |
| 4 | Average purchase: Standard vs Express shipping | Express **$60.48** vs Standard **$58.46** |
| 5 | Subscribers vs non-subscribers | Non-subscribers: 2,847 customers, avg $59.87, $170,436 revenue · Subscribers: 1,053 customers, avg $59.49, $62,645 revenue |
| 6 | Customer segments (CTE + `CASE`) | Loyal **3,116** · Returning **701** · New **83** |
| 7 | Revenue by age group | Young adult $62,143 · Middle-aged $59,197 · Adult $55,978 · Senior $55,763 |

**SQL techniques used:** aggregations (`SUM`, `AVG`, `COUNT`), `GROUP BY`, subqueries, CTEs (`WITH`), `CASE` expressions, `ROUND`, `ORDER BY` / `LIMIT`.

**Segmentation rules:** 1 previous purchase → `new` · 2–10 → `returning` · more than 10 → `loyal`.

---

## 📈 Power BI Dashboard

`customer behaviour.pbix` is a single-page interactive dashboard with:

- **KPI cards:** Number of Customers, Average Purchase Amount, Average Review Rating
- **Pie charts:** customers by subscription status, customers by payment method
- **Column charts:** purchase amount by category, customer count by category
- **Bar chart:** purchase amount by age group
- **Slicers:** subscription status, gender, category, shipping type

Slicers cross-filter every visual, so the whole page can be explored by customer attributes.

---

## 🔍 Key Takeaways

- **Male customers generate about 68 % of revenue** ($157,890 of $233,081).
- **Subscribing does not mean spending more:** average purchase is almost identical ($59.49 vs $59.87), and non-subscribers make up ~73 % of customers.
- **Discounts don't only attract low-value orders:** about half (839 / 1,677) of discount users spent at or above the overall average.
- **Express shipping** customers spend slightly more per order than Standard (+$2.02).
- **Age groups contribute fairly evenly**, with young adults (18–31) the largest at ~27 % of revenue.
- Most customers (~80 %) fall in the **loyal** segment under the current segmentation rules.

---

## 🛠 Tech Stack

| Tool | Purpose |
|---|---|
| Python, pandas | Data loading, cleaning, feature engineering |
| SQLAlchemy, psycopg2 | Connecting Python to PostgreSQL |
| PostgreSQL / pgAdmin | Storage and SQL analysis |
| Power BI | Interactive dashboard |

---

## 🚀 How to Run

1. **Clone the repo**
   ```bash
   git clone https://github.com/vishwanarayanaswamy/customer_behaviour_analysis.git
   cd customer_behaviour_analysis
   ```
2. **Install dependencies**
   ```bash
   pip install pandas sqlalchemy psycopg2-binary jupyter
   ```
3. **Create a PostgreSQL database** (e.g. `customer`) and set your own connection details in the notebook. Keep credentials out of version control, e.g. use an environment variable:
   ```python
   import os
   from sqlalchemy import create_engine

   engine = create_engine(
       f"postgresql+psycopg2://postgres:{os.environ['PGPASSWORD']}@localhost:5432/customer"
   )
   ```
4. **Run** `customer_data_exploration.ipynb` – this cleans the data and writes the `customer` table.
5. **Run the queries** in `customer_behaviour.sql` in pgAdmin or `psql`.
6. **Open** `customer behaviour.pbix` in Power BI Desktop and refresh the data source if needed.

---

---

## 👤 Author

**Vishwa Narayanaswamy**  
MSc Data Science, University of Europe for Applied Sciences  
GitHub: [@vishwanarayanaswamy](https://github.com/vishwanarayanaswamy)

# Customer Segmentation & Retail Trend Analysis

An end-to-end data analytics project that segments retail customers by value and
behavior, and surfaces the key drivers of revenue — built with Python, PostgreSQL,
SQL, and Power BI.

## Overview

Using a retail customer transaction dataset, this project answers a core business
question: **which customers matter most, and what distinguishes them?** Rather than
just describing the data, the analysis builds customer segments (by purchase
frequency and spend) and uses them to answer concrete questions — revenue
concentration, discount dependency, churn risk, and subscription behavior — that a
retail business could act on directly.

## Dataset

- **Source:** Retail customer shopping behavior dataset, 3,900 records, 18 original
  columns (customer demographics, purchase details, payment/shipping preferences,
  review ratings, subscription status).
- **File:** `customer_shopping_behavior.csv`

## Tech Stack

- **Python** (pandas) — data loading and cleaning
- **PostgreSQL** — relational storage for the cleaned dataset
- **SQLAlchemy / psycopg2** — Python-to-Postgres connection
- **SQL** — segmentation logic and analytical queries
- **Power BI** — interactive dashboard for stakeholder-facing visuals

## Project Structure

```
├── Customer_behavior.ipynb              # Data loading, cleaning, and load-to-Postgres
├── customer_shopping_behavior.csv       # Raw dataset
├── customer_segmentation_queries_v2.sql # 10 SQL queries: segmentation + analysis
├── Dashboard.pbix                       # Power BI dashboard
├── requirements.txt
└── README.md
```

## Data Cleaning (done in the notebook)

- Renamed all columns to snake_case for consistency with SQL usage.
- Identified that `discount_applied` and `promo_code_used` were identical across
  every row and dropped the redundant `promo_code_used` column.
- Derived `age_group` from raw age for demographic-level analysis.
- Derived `purchase_frequency_days` from the categorical `frequency_of_purchases`
  field for more granular downstream analysis.
- Loaded the cleaned DataFrame into a PostgreSQL table (`customer`) using
  SQLAlchemy.

## Customer Segmentation Approach

Customers are segmented two ways across the analysis:

1. **Loyalty segment** (New / Returning / Loyal), based on `previous_purchases`.
2. **Spend quintiles**, based on `purchase_amount`, used to measure revenue
   concentration (i.e. what share of revenue comes from top spenders).

## SQL Analysis & Results

All 10 queries live in `customer_segmentation_queries_v2.sql`. Results below are
from running them against the full 3,900-row dataset.

| # | Question | Key Result |
|---|---|---|
| 1 | Revenue by gender | Male: $157,890 vs. Female: $75,191 — male customers drive roughly 2/3 of total revenue |
| 2 | Loyalty segmentation | Loyal: 3,116 customers · Returning: 701 · New: 83 |
| 3 | Revenue concentration (spend quintiles) | Top 20% of customers contribute **30.99%** of total revenue; bottom 20% contribute just 9.12% |
| 4 | Revenue by age group | Fairly even split — Young Adult (26.66%), Middle-aged (25.40%), Adult (24.02%), Senior (23.92%) |
| 5 | Revenue per customer by location | Alaska leads at $67.60/customer, followed by Pennsylvania ($66.57) and Arizona ($66.55) |
| 6 | Top 3 products by revenue per category | Clothing: Blouse, Shirt, Dress · Accessories: Jewelry, Sunglasses, Belt · Footwear: Shoes, Sandals, Boots · Outerwear: Coat, Jacket |
| 7 | Discount usage by loyalty segment | Loyal customers use discounts *more*, not less — 43.65% vs. 37.35% for New customers |
| 8 | Subscribed vs. non-subscribed spend | Average spend is nearly identical ($59.49 vs. $59.87) — subscription status doesn't predict higher spend |
| 9 | Subscription rate among repeat buyers | Only 27.56% of repeat buyers (>5 previous purchases) are subscribed — most loyal customers aren't captured by the subscription program |
| 10 | High-spend, low-rating customers (churn risk) | 20 customers flagged: above-average spend paired with below-average satisfaction |

## Key Findings

- **Revenue is concentrated but not extreme**: the top spend quintile drives ~31% of
  revenue — a meaningful concentration, but nowhere near a classic 80/20 pattern,
  suggesting revenue here is spread across a broad base rather than a handful of
  whales.
- **Loyalty doesn't reduce discount dependency — it increases it.** Loyal customers
  have the *highest* discount usage rate (43.65%), which challenges the common
  assumption that loyal customers are less price-sensitive.
- **Subscription status is a weak spend signal.** Subscribed and non-subscribed
  customers spend almost the same on average, and fewer than 3 in 10 repeat buyers
  are even subscribed — this points to a gap between the loyalty program and actual
  loyal behavior worth investigating further.
- **Gender revenue split is heavily skewed** toward male customers (68% of total
  revenue), a pattern worth validating against category-level preferences before
  drawing business conclusions.

## Dashboard

`Dashboard.pbix` visualizes the segmentation and revenue findings above for
stakeholder review. *(Currently based on the base analysis; segmentation-specific
visuals — value-tier matrix, revenue concentration chart, at-risk customer table —
are a planned next iteration.)*

## How to Run

1. Install PostgreSQL and create a database named `customer_behavior`.
2. Install dependencies: `pip install -r requirements.txt`
3. Open `Customer_behavior.ipynb` in Jupyter, update the `password` variable with
   your own Postgres password, and run all cells to load the data.
4. Open `customer_segmentation_queries_v2.sql` in pgAdmin (or any Postgres client)
   and run the queries against the `customer` table.
5. Open `Dashboard.pbix` in Power BI Desktop to view/edit the dashboard.

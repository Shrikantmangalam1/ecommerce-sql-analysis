# 🛒 Global E-Commerce SQL Analysis

A comprehensive SQL portfolio project analyzing a large-scale global e-commerce database covering **30 queries** from basic SELECT statements to advanced window functions, CTEs, and business analytics.

---

## 📋 Table of Contents
- [Project Overview](#project-overview)
- [Dataset](#dataset)
- [Database Schema](#database-schema)
- [Setup Instructions](#setup-instructions)
- [Queries](#queries)
- [Key Findings](#key-findings)
- [Data Limitations](#data-limitations)
- [Skills Demonstrated](#skills-demonstrated)
- [Tech Stack](#tech-stack)

---

## 📌 Project Overview

This project simulates the work of a data analyst at a global e-commerce company. The analysis covers:

- Revenue and profitability analysis
- Customer segmentation and behavior
- Churn detection and retention insights
- Fraud risk identification
- Payment failure analysis
- Shipping and logistics performance
- Product performance ranking
- Time series and trend analysis

---

## 📦 Dataset

| Property | Details |
|---|---|
| Source | [Kaggle — Global E-Commerce Dataset +1M Records](https://www.kaggle.com/datasets/akrambelha/global-e-commerce-dataset-1m-records-20242026) |
| Records | ~1 million orders (2024–2026) |
| Type | Synthetically generated |
| Format | PostgreSQL dump (.sql) |
| Size | ~356MB |
| Tables | 10 relational tables |

> **Note:** The dataset is synthetically generated. All SQL logic and analytical methodology are production-ready and would generate meaningful insights on real-world data.

---

## 🗄️ Database Schema

```
customers ──────────── orders ──────────── order_items
                          │                     │
                          │                     │
                       payments              products
                          │
                     ┌────┴─────┐
                  shipping   marketing
                          │
                  ┌───────┴────────┐
             risk_management   user_behavior
                          │
                        reviews
```

### Tables

| Table | Description | Key Columns |
|---|---|---|
| `customers` | Customer demographics and segments | customer_id, customer_segment, country, loyalty_score |
| `orders` | Order records with date info | order_id, customer_id, order_year, order_month, order_day |
| `order_items` | Line items per order | order_id, product_id, quantity, total_price_usd, profit_usd |
| `products` | Product catalog | product_id, product_name, category, product_rating_avg |
| `payments` | Payment details | order_id, payment_method, payment_status |
| `shipping` | Shipping and delivery info | order_id, shipping_method, delivery_days, shipping_cost_usd |
| `reviews` | Product reviews | order_id, product_id, rating, customer_feedback |
| `marketing` | Campaign and coupon data | order_id, coupon_used, coupon_code, campaign_source |
| `user_behavior` | Session and device data | order_id, device_type, session_duration_minutes |
| `risk_management` | Fraud risk scores | order_id, fraud_risk_score, order_priority |

---

## 🛠️ Setup Instructions

### Prerequisites
- PostgreSQL 14+
- pgAdmin 4 (or any PostgreSQL client)

### Step 1 — Create the Database
```sql
CREATE DATABASE ecommerce_db;
```

### Step 2 — Create Tables
Run the schema file to create all tables:
```bash
psql -U postgres -d ecommerce_db -f schema/create_tables.sql
```

### Step 3 — Import the Dataset
Download the dataset from [Kaggle](https://www.kaggle.com/datasets/akrambelha/global-e-commerce-dataset-1m-records-20242026) and import:
```bash
psql -U postgres -d ecommerce_db -f ecommerce_db.sql
```

> ⚠️ **Important:** The raw dataset contains duplicate primary keys on `order_id`, `product_id`, and `customer_id`. Use a staging table approach to deduplicate before importing into the main schema. See [Data Limitations](#data-limitations) for details.

### Staging Table Approach for Deduplication
```sql
-- Step 1: Create staging table (no constraints)
CREATE TABLE orders_staging AS TABLE orders WITH NO DATA;

-- Step 2: Import raw data into staging
-- (Copy data from dump into staging tables)

-- Step 3: Insert deduplicated data into real table
INSERT INTO orders
SELECT DISTINCT ON (order_id) *
FROM orders_staging
ORDER BY order_id;

-- Step 4: Drop staging table
DROP TABLE orders_staging;
```

---

## 📝 Queries

### 🟢 Easy Level — `queries/easy_questions.sql`

| # | Question | Concepts |
|---|---|---|
| 1 | List all customers and their country | SELECT, FROM |
| 2 | Find all female customers | WHERE |
| 3 | Show all returned orders | WHERE, IS NOT NULL |
| 4 | Products sorted by rating | ORDER BY |
| 5 | Total customer count | COUNT |
| 6 | Orders placed in 2024 | WHERE, year filter |
| 7 | Top 10 most expensive products | ORDER BY, LIMIT |
| 8 | Distinct customer countries | DISTINCT |
| 9 | Customer count per country | GROUP BY, COUNT |
| 10 | Electronics category products | WHERE, string filter |

### 🟡 Medium Level — `queries/medium_questions.sql`

| # | Question | Concepts |
|---|---|---|
| 1 | Total revenue per product category | JOIN, GROUP BY, SUM |
| 2 | Order count per customer segment | JOIN, GROUP BY, COUNT |
| 3 | Average delivery days by shipping method | GROUP BY, AVG |
| 4 | Top 5 best-selling products by quantity | JOIN, GROUP BY, SUM, LIMIT |
| 5 | Total profit and revenue per year | JOIN, GROUP BY, multiple aggregates |
| 6 | Most common VIP payment method | 3-table JOIN, GROUP BY |
| 7 | Low rated products with many reviews | HAVING, GROUP BY |
| 8 | Coupon used but payment failed | JOIN, WHERE, multiple conditions |
| 9 | Lost revenue from failed coupon orders | 3-table JOIN, SUM, COUNT |
| 10 | Average order value per customer | JOIN, GROUP BY, AVG |

### 🔴 Hard Level — `queries/hard_questions.sql`

| # | Question | Concepts |
|---|---|---|
| 1 | Rank customers by total spending | CTE, RANK(), window functions |
| 2 | Month-over-month revenue growth | CTE, LAG(), date arithmetic |
| 3 | Customers active every month of 2024 | GROUP BY, HAVING, COUNT DISTINCT |
| 4 | Products in top 10 revenue for 3+ months | CTE, RANK(), PARTITION BY |
| 5 | Cumulative revenue running total | SUM() OVER, window frame |
| 6 | Churned customers (inactive 6+ months) | CTE, MAKE_DATE, INTERVAL |
| 7 | First and last order per customer | MIN, MAX, DATE_PART, CASE WHEN |
| 8 | Fraud risk top 5% per country | PERCENT_RANK(), PARTITION BY |
| 9 | Products with 3 consecutive months margin growth | LAG(1), LAG(2), PARTITION BY |
| 10 | 3-month rolling average revenue by category | AVG() OVER, window frame |

---

## 📊 Key Findings

### Revenue Analysis
| Category | Total Revenue |
|---|---|
| Electronics | $104,138,855 |
| Home | $65,329,673 |
| Sports | $54,355,229 |
| Health | $42,765,644 |
| Clothing | $33,754,419 |

> Electronics is the highest-grossing category, outperforming the next closest (Home) by approximately 59%.

---

### Year-over-Year Revenue
| Year | Total Revenue | Total Profit |
|---|---|---|
| 2024 | $136,822,045 | $54,614,287 |
| 2025 | $149,990,667 | $59,857,425 |
| 2026 | $13,531,108* | $5,402,758* |

*2026 is partial data (Jan–Feb only)

> Revenue grew approximately 10% from 2024 to 2025 while profit margins remained stable at ~40%, suggesting efficient scaling.

---

### Customer Segments
| Segment | Total Orders | Share |
|---|---|---|
| Regular | 446,152 | ~59% |
| Premium | 223,643 | ~30% |
| VIP | 74,648 | ~10% |

---

### 💳 Failed Payment Analysis
| Metric | Value |
|---|---|
| Orders with coupon + failed payment | 18,513 |
| Lost revenue | $7,445,393.63 |

> 18,513 orders where customers applied a coupon still resulted in failed payments, representing $7.4M in lost revenue — a direct and quantifiable recovery opportunity.

---

### 📉 Churn Analysis
| Metric | Value |
|---|---|
| Churned customers (inactive 6+ months) | 555,936 |
| Churn rate | ~70% |
| Reference date | February 2026 |
| Inactivity threshold | August 2025 |

---

### 🚨 Fraud Risk Analysis
| Metric | Value |
|---|---|
| High risk orders (top 5% per country) | 37,581 |
| Detection method | PERCENT_RANK() per country |

---

### 📈 Cumulative Revenue
| Milestone | Reached At |
|---|---|
| $100M | ~October 2024 |
| $150M | ~January 2025 |
| $200M | ~June 2025 |
| $250M | ~September 2025 |
| $300M | February 2026 |

---

## ⚠️ Data Limitations

This project transparently documents all data quality issues identified during analysis:

| Issue | Finding | Impact |
|---|---|---|
| Duplicate primary keys | Raw dump had duplicate order_id, product_id, customer_id | ~25% rows removed during deduplication |
| Synthetic distribution | All shipping methods averaged identical delivery times (~7.5 days) | No meaningful shipping performance comparison |
| Uniform product sales | Top selling products all showed identical quantity sold (5 units) | Product ranking analysis limited |
| Partial year data | 2024 starts March, 2026 ends February | YoY comparisons exclude partial periods |
| Uniform payment distribution | All payment methods showed near-equal usage | No dominant payment preference detectable |

> All SQL logic and analytical methodology are valid and production-ready. Results would generate meaningful business insights on a real-world dataset with organic distributions.

---

## 💡 Skills Demonstrated

### SQL Concepts
- **Joins:** INNER JOIN, LEFT JOIN, multi-table joins (3+ tables)
- **Aggregations:** SUM, COUNT, AVG, MIN, MAX, ROUND
- **Filtering:** WHERE, HAVING, IS NULL, IS NOT NULL
- **Grouping:** GROUP BY, DISTINCT, COUNT DISTINCT
- **Subqueries:** Correlated subqueries, scalar subqueries
- **CTEs:** Single CTEs, chained CTEs, multiple CTEs
- **Window Functions:** RANK(), DENSE_RANK(), ROW_NUMBER(), LAG(), LEAD(), PERCENT_RANK(), SUM() OVER, AVG() OVER
- **Window Frames:** ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
- **Date Functions:** MAKE_DATE(), DATE_PART(), AGE(), INTERVAL
- **Type Casting:** CAST(), :: operator
- **Conditional Logic:** CASE WHEN, COALESCE

### Analytical Skills
- Revenue and profitability analysis
- Customer segmentation and lifecycle analysis
- Churn detection and retention analysis
- Fraud risk identification
- Time series and trend analysis
- Data quality identification and documentation
- Business insight generation from raw data

---

## 🔧 Tech Stack

| Tool | Purpose |
|---|---|
| PostgreSQL 14+ | Database engine |
| pgAdmin 4 | Query execution and database management |
| Git | Version control |
| GitHub | Portfolio hosting |

---

## 📁 Repository Structure

```
ecommerce-sql-analysis/
│
├── README.md                    ← Project documentation
│
├── schema/
│   └── create_tables.sql        ← Table definitions and constraints
│
├── queries/
│   ├── easy_questions.sql       ← Basic SQL (10 queries)
│   ├── medium_questions.sql     ← Intermediate SQL (10 queries)
│   └── hard_questions.sql       ← Advanced SQL (10 queries)
│
└── insights/
    └── key_findings.md          ← Detailed business insights
```

---

## 🔗 Connect

Feel free to connect or reach out if you have questions about the project or the SQL methodology used.

---

*This project was built as part of a SQL portfolio to demonstrate proficiency from basic queries to advanced analytical SQL using a real-world ecommerce schema.*

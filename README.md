# pharma_insights — Pharmacy Sales Analytics (PostgreSQL)

## Overview

pharma_insights is an end-to-end SQL project that transforms a raw, denormalised pharmacy transactions dataset into a structured star-schema model using PostgreSQL. The project then performs exploratory data analysis (EDA) to identify revenue drivers across channels, subchannels, time, and product classes.

This project demonstrates:

- Data cleaning and standardisation
- Data type correction and validation
- Date engineering from fragmented fields
- Star schema design with foreign key enforcement
- Analytical querying using joins and aggregations
- Business insight extraction from structured data

---

## Business Objectives

The analysis answers the following key questions:

1. Which channel drives the most revenue?
2. Which subchannels contribute most to total sales?
3. How has revenue trended from 2022–2025?
4. Which product classes generate the highest revenue?
5. Which product classes have the highest demand (volume)?

---

## Data Preparation

### Cleaning & Standardisation

- Standardised inconsistent customer names using CASE WHEN logic
- Applied TRIM() to remove whitespace inconsistencies
- Removed duplicate logical entities
- Ensured consistent formatting across text-based fields

### Type Conversion

Several fields were originally stored as TEXT and converted to NUMERIC to enable accurate aggregation:

- quantity → NUMERIC
- price → NUMERIC
- sales → NUMERIC
- latitude / longitude → NUMERIC

### Date Engineering

The dataset stored months and years separately. A proper date column (record_date) was created using:

TO_DATE(TRIM(years) || ' ' || TRIM(months) || ' 01', 'YYYY Month DD')

This enabled accurate time-series analysis.

---

## Data Model (Star Schema)

The database was normalised into a star-schema structure.

### Dimension Tables
- customertable
- producttable
- distributortable
- staff

### Fact Table
- sales (transaction-level grain)

The sales table enforces referential integrity through foreign keys linking to all dimension tables.

This structure supports scalable BI integration and efficient querying.

---

## Key Insights

### Channel Performance

The Pharmacy channel outperformed the Hospital channel in 2025, making it the primary revenue driver in the most recent reporting period.

---

### Subchannel Performance

Revenue ranking across subchannels:

1. Retail
2. Government
3. Institution
4. Private

Retail is the strongest commercial segment, while the Private segment underperforms relative to peers.

---

### Revenue Trend (2022–2025)

Time-series analysis revealed:

- Revenue peaked in 2023
- Declined in 2024
- Reached its lowest level in 2025

This sustained post-2023 decline suggests structural performance pressures requiring further investigation.

---

### Product Class Revenue

- Analgesics generated the highest total revenue.
- Followed by Antiseptics and Mood Stabilizers.

Core therapeutic categories dominate overall business performance.

---

### Product Demand (Volume)

Volume aggregation confirmed:

- Analgesics recorded the highest total units sold.
- Revenue leadership aligns with demand leadership.

This indicates that revenue strength is volume-driven rather than price-driven.

---

## Strategic Interpretation

The SQL analysis highlights three critical business levers:

- Protect and optimise Retail pharmacy distribution.
- Investigate revenue decline following the 2023 peak.
- Prioritise supply chain stability for high-demand product classes, especially Analgesics.

---

## Technical Skills Demonstrated

- PostgreSQL
- Data cleaning and transformation
- Schema design and normalisation
- Foreign key constraints
- Multi-table joins
- Aggregations and grouping
- Time-series analysis using EXTRACT
- Structured SQL scripting

---

## SUMMARY OF PROJECT WORK FLOW

1. Created a PostgreSQL database (e.g., pharma_insights).
2. Loaded raw data into pharmacy_records.
3. Executed schema creation scripts.
4. Ran cleaning and transformation scripts.
5. Executed analytical queries in Key_SQL_Queries.sql.

---



---

# 📂 Key_SQL_Queries.sql 

```sql
-- ============================================
-- 1️⃣ Channel Revenue (2025)
-- ============================================

SELECT channel,
       SUM(sales) AS revenue
FROM sales
WHERE EXTRACT(YEAR FROM record_date) = 2025
GROUP BY channel
ORDER BY revenue DESC;


-- ============================================
-- 2️⃣ Subchannel Performance (Pharmacy Only)
-- ============================================

SELECT subchannel,
       SUM(sales) AS revenue
FROM sales
WHERE channel = 'Pharmacy'
GROUP BY subchannel
ORDER BY revenue DESC;


-- ============================================
-- 3️⃣ Overall Subchannel Ranking
-- ============================================

SELECT subchannel,
       SUM(sales) AS revenue
FROM sales
GROUP BY subchannel
ORDER BY revenue DESC;


-- ============================================
-- 4️⃣ Revenue Trend by Year
-- ============================================

SELECT EXTRACT(YEAR FROM record_date) AS sales_year,
       SUM(sales) AS revenue
FROM sales
GROUP BY sales_year
ORDER BY sales_year;


-- ============================================
-- 5️⃣ Revenue by Product Class
-- ============================================

SELECT p.productclass,
       SUM(s.sales) AS revenue
FROM sales s
JOIN producttable p
  ON s.productid = p.productid
GROUP BY p.productclass
ORDER BY revenue DESC;


-- ============================================
-- 6️⃣ Demand (Volume) by Product Class
-- ============================================

SELECT p.productclass,
       SUM(s.quantity) AS volume
FROM sales s
JOIN producttable p
  ON s.productid = p.productid
GROUP BY p.productclass
ORDER BY volume DESC;


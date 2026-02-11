# pharma_insights — Pharmacy Sales Analytics (PostgreSQL)

## Overview
This project transforms a raw, denormalised pharmacy transaction dataset into a structured star-schema model using PostgreSQL, followed by exploratory SQL analysis to identify revenue drivers across channels, product classes, and time.

The objective was to demonstrate end-to-end SQL capability:
- Data cleaning & standardisation
- Type conversion & date engineering
- Normalisation into dimension & fact tables
- Foreign key enforcement
- Analytical querying (EDA)

---

## Business Questions

1. Which channel drives the most revenue?
2. Which subchannels contribute most to total sales?
3. How does revenue trend from 2022–2025?
4. Which product classes generate the highest revenue?
5. Which product classes are most in demand (volume)?

---

## Data Preparation

### Cleaning
- Standardised inconsistent customer names using `CASE WHEN`
- Trimmed whitespace across text fields
- Removed duplicate logical entities

### Type Conversion
Converted TEXT fields to numeric types:
- quantity → NUMERIC
- price → NUMERIC
- sales → NUMERIC
- latitude / longitude → NUMERIC

### Date Engineering
Created `record_date` from separate `months` and `years` columns using:

```sql
TO_DATE(TRIM(years) || ' ' || TRIM(months) || ' 01', 'YYYY Month DD')

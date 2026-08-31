# Pipeline1
# American Cheese Retail Price Pipeline

## Overview
A data pipeline built in Databricks (pandas + Spark/Delta) that ingests raw retail 
price data for American cheese across 12 US metro areas, cleans and standardizes it, 
and produces weekly city-level and brand-level price aggregations.

## Dataset
Source: Kaggle — costinflation American Cheese Retail Prices (2026-07-13 to 2026-08-10)
link -- https://www.kaggle.com/datasets/costinflation/american-cheese-prices-raw-dataset-2026?select=costinflation-american-cheese-retail-prices-raw-2026-07-13-to-2026-08-10.csv

## Pipeline Steps
1. **Ingest** — load raw CSV into pandas
2. **Data Quality Checks** — nulls, duplicates, date parsing, price outlier scan
3. **Null Handling** — traced nulls in `normalized_price_amount` to listings with 
   unknown package weight; flagged rather than dropped, to preserve raw price data
4. **Feature Engineering** — extracted `brand` and `cheese_form` from free-text 
   product names
5. **Aggregation** — weekly avg price/lb by city; avg/min/max/count by brand
6. **Storage** — wrote cleaned data + aggregations as Delta tables

## Key Findings
- Weekly avg prices across cities landed mostly in the $5.70–$6.50/lb range
- Borden priced notably higher than average (~$10/lb); Amazon listings showed an 
  unusually low, narrow price range worth further investigation
- ~8.5% of rows lacked a usable per-pound price due to missing package weight info

## Tech Stack
Databricks Free Edition, Python, pandas, PySpark, Delta Lake

## Pipeline Work flow
**                 **RAW CSV**
                    │
                    ▼
         ** Databricks Volume**
                    │
                    ▼
            ** Pandas DataFrame****
                    │
          ┌─────────┴─────────┐
          ▼                   ▼
  **  Data Profiling       Data Cleaning**
          │                   │
          │            ┌──────┴──────┐
          │     **       ▼             ▼
          │        Brand         Cheese Form
          │       Extraction     Extraction
          │            │             │
          └────────────┴─────────────┘
                       │
                       ▼
                 Aggregations
                  /         \
                 ▼           ▼
          City + Week      Brand
             Average      Statistics
                 │           │
                 └─────┬─────┘
                       ▼
                Spark DataFrame
                       │
                       ▼
                  Delta Tables**

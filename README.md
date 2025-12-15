__Multi-Currency E-Commerce Lakehouse on Databricks__

__Business problem__

An e-commerce company sells across multiple countries and currencies, and leadership needs a trusted, daily-updated source of truth for revenue, discounts, coupons, and demand patterns. Raw order-line data arrives as daily files and contains typical issues like inconsistent codes, mixed data types, duplicates in dimensions, missing customer fields, and inconsistent channel/currency formatting.

__What this pipeline enables__

This lakehouse pipeline converts raw daily order-line files into governed, analytics-ready Gold marts so teams can answer questions like:

Revenue & growth: What is net sales by month/quarter, and how is it trending?

Discount effectiveness: How much revenue is driven by coupons, and what is the discount impact by category/brand?

Channel performance: How do website vs mobile app sales compare over time?

Operational patterns: When do customers buy most (hour-of-day, weekday vs weekend)?

Global reporting: What is total revenue after currency normalization into a single reporting currency (INR equivalent)?

__Who uses it__

Business stakeholders: self-serve KPIs and trends through dashboards and Genie Q&A

Data analysts: reliable, denormalized dataset for BI without repeated joins

Data engineers: scalable medallion design that supports backfills and scheduled daily runs
Daily order_items CSV (Aug–Oct) → Delta Medallion (Raw → Bronze → Silver → Gold) + BI Dashboards + Genie NLQ

__Overview__

This project builds a production-style Databricks Lakehouse pipeline for an e-commerce dataset stored as CSV files. It ingests daily order_items order-line files (3 months: Aug, Sep, Oct, ~183K rows) plus supporting dimension tables (products, brands, categories, customers, calendar/date). Data is processed through a Delta Lake Medallion architecture (Raw → Bronze → Silver → Gold) and published as business-ready Gold marts used for dashboards and self-serve analytics.

Key features include data quality engineering, dimensional modeling, currency normalization (multi-currency → USD equivalent), and Databricks Genie for natural-language analytics.

__Dataset__

__Source tables (CSV)__

_Fact (order lines)_

order_items (daily landing files, Aug–Oct)

_Dimensions_

brands (brand_code, brand_name, category_code)

categories (category_code, category_name)

products (product attributes + brand_code + category_code)

customers (customer profile fields + country/state)

calendar/date (date attributes like year/quarter/month/day)

_Business characteristics captured_

Multi-currency transactions (example: USD, GBP, AUD, INR)

Discounts and coupons

Taxes

Channel (web vs app)

Time analysis (day/month/quarter/weekend, hour-of-day)

__Architecture__

_Raw (Unity Catalog Volume)_

CSVs stored as-is in a managed Unity Catalog volume (source_data.raw)

Acts as the landing zone (no transformations)

_Bronze (Delta “structured raw”)_

Convert CSV → Delta tables

Add ingestion metadata for auditability (source file path, ingest timestamp)

_Silver (Clean + standardized)_

Apply data quality rules and enforce types (dates, timestamps, numeric columns)

Normalize codes and values (uppercase, consistent mappings)

Deduplicate dimensions and apply null-handling based on business logic

_Gold (Business-ready marts)_

Build curated dimensions and fact tables for analytics

Add derived business metrics and descriptive columns

Normalize revenue into a single currency (INR equivalent)

<img width="1915" height="657" alt="image" src="https://github.com/user-attachments/assets/83b28b93-762a-45a4-9feb-f7ff8bb5862b" />

__Orchestration (Production Pattern)__

This repo is implemented as a historical backfill (manual notebook runs). In production, the same structure supports daily incremental processing via Databricks Jobs/Workflows:

Dimensions: Bronze → Silver → Gold

Facts: Bronze → Silver → Gold

Trigger: schedule (ex: nightly) or file arrival

<img width="1038" height="261" alt="image" src="https://github.com/user-attachments/assets/d37c0f75-096b-4406-8ac5-4ab1fbd27ec4" />

<img width="755" height="222" alt="image" src="https://github.com/user-attachments/assets/029741dc-9d28-4ece-ba2f-17333b301d6d" />


__Tech Stack__

Databricks (Notebooks, SQL Editor, Dashboards, Jobs/Workflows)

PySpark + Spark SQL

Delta Lake

Unity Catalog (Catalogs, Schemas, Managed Volumes)

Genie (NLQ → SQL)

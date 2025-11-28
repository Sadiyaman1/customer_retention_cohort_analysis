# E-Commerce Customer Cohort Analysis (ELT: BigQuery, Fivetran & Databricks)

## Project Overview

This repository documents the end-to-end data pipeline and analytical workflow for a **detailed customer cohort analysis** in the e-commerce domain.

The main goal is to measure **customer retention** and **repeat purchase behavior**. Customers are segmented based on the month of their first purchase (the cohort).

All metric calculations are performed using data transformations in Databricks, and the final metric table (`final_db_table`) is optimized for direct use in dashboards.

---

## Data Pipeline & Architecture (ELT Workflow)
![Fivetran Sync Konfiguration Screenshot](config/fivetran_config_screenshot.png)

The data flows through an automated **ELT** process (Extract, Load, Transform) across the following components:

### 1. Extract & Source: Google BigQuery (with GCS Staging)

* **Data Ingestion:** Raw transactional data (`ecom_orders.csv`) is first uploaded to **Google Cloud Storage (GCS)** using a Python script (Google Cloud Storage Client).
* **Data Warehouse Setup:** A BigQuery dataset (`hybrid-sentry-479215-n9.cohort_analysis`) is created, and the CSV data is loaded into the BigQuery table (`ecom_orders`) using the **`LOAD DATA`** command. BigQuery serves as the primary, reliable source system.

### 2. Load: Fivetran (Data Replication)

* **Role:** Fivetran acts as the EL layer, automatically extracting the raw data from BigQuery (`bigquery_db` connector).
* **Destination:** Data is loaded into the **Databricks Unity Catalog/Warehouse**.
* **Configuration:** The table `ecom_orders` is replicated using **Soft Delete Mode**, which maintains historical records without physically deleting rows in the target table.

### 3. Transform & Analytics: Databricks (Spark SQL)

* **Cohort Identification:** Determining the **first purchase month** (`cohort_month`) for each customer (`customer_id`) as the basis for cohort grouping.
* **Retention Analysis (Time-Based):** Computing **retention counts** (`retained_1m_count`, `retained_2m_count`, etc.) based on the time between a customer’s first and second purchase.
* **Repeat Analysis (Order-Based):** Counting customers who made **at least 2, 3, or 4 purchases** over time (`repeat_count_2plus`, etc.).
* **Revenue & Order Contribution:** Aggregating **total cohort revenue** (`cohort_sales`) and **total cohort orders** (`cohort_orders`).
* **KPI Normalization:** Calculating the proportional contributions relative to the entire customer base (`cohort_size_total_customers_ratio`, etc.).
* **Creation of the Final Table:** Merging all key metrics into a final dashboard-ready table (`final_db_table`) for use in BI tools.

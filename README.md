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


---

## Repository Structure

| Directory / File | Description |
| :--- | :--- |
| **`config/`** | Configuration files (e.g., Fivetran setup). |
| **`dashboard & report/`** | Dashboard exports and project reports. |
| **`data/source/`** | Raw input data (`ecom_orders.csv`). |
| **`notebooks/`** | All project notebooks for data processing. |
| ├── `data_ingestion/` | BigQuery setup and ingestion notebook. |
| └── `databricks_transformation/` | Databricks transformation and analysis notebook. |
| **`.gitignore`** | Files excluded from version control. |
| **`LICENSE`** | Project license. |
| **`README.md`** | Main project documentation. |


---

## Tools & Technologies Used

This project integrates several modern data engineering and analytics tools:

| Component | Tool / Technology | Purpose |
|----------|-------------------|---------|
| Cloud Storage | **Google Cloud Storage (GCS)** | Staging raw CSV files before warehouse loading |
| Data Warehouse | **Google BigQuery** | Primary storage for raw source data |
| EL Replication | **Fivetran** | Automated extraction & loading of source tables into Databricks |
| Lakehouse Platform | **Databricks (Unity Catalog, SQL Warehouse)** | Transformation layer, cohort computations, metric engineering |
| Notebook Environment | **Databricks Notebooks (Python + SQL)** | Step-by-step transformations and logic explanation |
| Dashboarding | **Databricks Dashboards v3** | Visualization of retention and repeat purchase KPIs |


---

## Dashboard

![Dashboard Screenshot](dashboard & report/Dashboard_Screenshot.pdf)

**Dashboard Link:**  
🔗 https://dbc-b854569c-8de6.cloud.databricks.com/dashboardsv3/01f0cae796b712dc8428349afbb05e1e/published?o=4166173051620611

---

## Documentation & Reproducibility (Reproduzierbarkeit)

All transformation steps, SQL logic, and explanations are documented in detail within the two notebooks included in this repository:

### 1. **`Cohort_Analysis_Data_Setup_(bigquery).ipynb`**

* Contains the full data ingestion and **setup workflow in Google BigQuery**.
* Includes **dataset creation**, initial **CSV loading** (via GCS), and preparation of the final **source schema** for Fivetran replication.

### 2. **`Cohort_Analysis_Transformation_(Databricks).ipynb`**

* Includes all **transformation logic** performed in **Databricks (Spark SQL)**.
* Covers **cohort identification**, **retention and repeat purchase calculations**, **KPI normalization**, and creation of the **final analytical table**.

Together, these notebooks provide a complete and transparent walkthrough of the entire **ELT process**—from data ingestion to the final analytical dataset and dashboard.

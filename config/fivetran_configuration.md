# Fivetran Configuration (Beginner's Guide)

## 1. Core Systems (Source & Destination)

| Parameter | Value | Meaning |
| :--- | :--- | :--- |
| **Source Database** | `BigQuery` | This is where we fetch the raw data from. |
| **Target Database** | `Databricks` | This is where we store and run the analysis. |

---

## 2. Table Mapping (What is Transferred)

| Source Table (BigQuery) | Target Path (Databricks) | Mode |
| :--- | :--- | :--- |
| `ecom_orders` | `workspace.bigquery_db_cohort_analysis` | **Automatic Sync** |

---

## 3. Data Deletion and History

* **Setting:** Data is synchronized using **Soft Delete Mode**.
* **Meaning:** If you delete a row in BigQuery, it won't be permanently erased in Databricks. Instead, it will be marked with a column (e.g., `_fivetran_deleted = TRUE`). This is crucial for preserving historical data necessary for your **Cohort Analysis**.

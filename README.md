#  E-Commerce Customer Cohort Analysis (ELT: BigQuery, Fivetran & Databricks)

## Projektübersicht

Dieses Repository dokumentiert die End-to-End-Pipeline und die analytische Verarbeitung für eine **detaillierte Kunden-Kohortenanalyse** (`Cohort Analysis`) im E-Commerce-Bereich.

Das Hauptziel ist es, die **Kundenbindung** (`Retention`) und das **Wiederholungskaufverhalten** (`Repeat Purchase Rate`) unserer Kunden zu messen und darzustellen. Dazu werden Kunden nach ihrem ersten Kaufmonat (der Kohorte) segmentiert.

Die gesamte Metrikberechnung erfolgt durch Data Transformation in Databricks, wobei die finale Metrik-Tabelle (`final_db_table`) zur direkten Verwendung in Dashboards dient.

---

## Data-Pipeline und Architektur (ELT-Workflow)

Die Daten fließen in einem automatisierten **ELT**-Prozess (Extract, Load, Transform) durch folgende Komponenten:

### 1. Extract & Source: Google BigQuery (mit GCS Staging)

* **Datenaufnahme (Ingestion):** Die rohen Transaktionsdaten (`ecom_orders.csv`) werden zunächst mittels Python (Google Cloud Storage Client) in den **Google Cloud Storage (GCS)** hochgeladen.
* **Data Warehouse Setup:** Anschließend wird ein BigQuery-Dataset (`hybrid-sentry-479215-n9.cohort_analysis`) erstellt und die CSV-Daten mittels des **`LOAD DATA`**-Befehls in die BigQuery-Zieltabelle (`ecom_orders`) geladen. BigQuery dient als primäre, zuverlässige Datenquelle.

### 2. Load: Fivetran (Datenreplikation)
* **Rolle:** Fivetran wird als EL-Tool (Extract & Load) eingesetzt, um die Rohdaten **automatisch** aus BigQuery (`bigquery_db` Connector) zu extrahieren.
* **Destination:** Die Daten werden in das **Databricks Unity Catalog/Warehouse** geladen.
* **Konfiguration:** Die Tabelle `ecom_orders` wird repliziert. Dabei wird der **Soft Delete Mode** verwendet, um historische Daten zu verwalten, ohne Zeilen physisch aus der Zieltabelle zu löschen.

### 3.  Transform & Analytics: Databricks (Spark SQL)
* **Identifikation der Kohorte:** Bestimmung des **ersten Kaufmonats** (`cohort_month`) für jeden Kunden (`customer_id`) als Basis für die Gruppierung.
* **Retention-Analyse (Zeitbasis):** Berechnung der **Kundenbindungs-Counts** (`retained_1m_count`, `retained_2m_count`, etc.) basierend auf der Zeit zwischen erstem und zweitem Kauf.
* **Repeat-Analyse (Order-Basis):** Zählung der Kunden, die **mindestens 2, 3 oder 4 Käufe** über die gesamte Zeit getätigt haben (`repeat_count_2plus`, etc.).
* **Umsatz- und Bestellbeitrag:** Aggregation des **Gesamtumsatzes** (`cohort_sales`) und der **Gesamtbestellungen** (`cohort_orders`) pro Kohorte.
* **KPI-Normalisierung:** Berechnung der prozentualen Anteile an der Gesamtbasis (`cohort_size_total_customers_ratio`, etc.).
* **Erstellung der Finalen Tabelle:** Zusammenführung aller Kennzahlen in die finale Dashboard-Tabelle (`final_db_table`) zur Verwendung in BI-Tools.

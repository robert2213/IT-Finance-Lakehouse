# IT-Finance-Lakehouse
Enterprise IT financial data pipeline — Delta Lake medallion architecture with Kimball Star Schema on Databricks
# IT Financial Lakehouse — Enterprise Spend Analytics

## Business Problem

Enterprise IT organizations managing $50M+ in multi-cloud and vendor spend across dozens of cost centers face a common challenge: financial data lives in disconnected systems — SAP general ledger extracts, AWS/Azure billing exports, and ServiceNow asset records — with no unified layer for variance analysis or executive reporting.

This project builds a production-grade data pipeline that consolidates fragmented IT spend data into a single, auditable analytical platform with executive-ready reporting.

---

## Architecture

![Architecture](architecture.png)

**Medallion Architecture on Databricks Delta Lake**

| Layer | Purpose | Tables |
|---|---|---|
| Bronze | Append-only raw ingestion | raw_vendor_transactions, raw_cost_centers |
| Silver | Clean, deduplicate, surrogate keys | clean_vendor_transactions, clean_cost_centers |
| Gold | Kimball Star Schema | fact_it_spend_monthly, dim_vendor, dim_cost_center |

---

## Tech Stack

- **Platform:** Databricks (PySpark, Delta Lake)
- **Modeling:** Kimball dimensional modeling (Star Schema)
- **Reporting:** Power BI (DirectLake mode)
- **Languages:** Python, SQL
- **Key Patterns:** Medallion architecture, MD5 surrogate keys, set-based transformations, window functions

---

## Notebooks

| Notebook | Description |
|---|---|
| 01_bronze_ingestion | Raw data ingestion and Delta table creation |
| 02_validation_queries | Data integrity checks across all medallion layers |

---

## Key Design Decisions

**Why medallion architecture?**
Cloud billing data arrives with frequent schema drift. Isolating raw files in Bronze protects historical data while the Gold Star Schema allows Power BI to aggregate millions of rows in milliseconds.

**Why surrogate keys?**
Source system entity names change over time (e.g. "Cloud Ops - 404" becomes "Cloud Operations"). MD5-hashed surrogate keys on natural key combinations ensure dimensional integrity across historical records without brittle string matching.

**Why set-based transformations?**
Financial variance and overhead allocation logic is implemented via window functions and vectorized PySpark operations rather than row-by-row iteration — directly translating enterprise FP&A patterns into scalable data engineering.

---

## Business Output

- Total IT spend consolidated across AWS, Azure, CrowdStrike, ServiceNow, Databricks, Salesforce
- Spend visibility by cost center, department, vendor, and forecast cycle (3+9, 6+6, 9+3)
- Executive rollup: Infrastructure $611K vs Corporate IT $186K in FY2026 Q1
- Pipeline certified via three-layer lineage audit with zero data loss Bronze → Gold

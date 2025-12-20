# 📓 Databricks – Stock Data Extraction Notebooks

This section documents the Databricks notebooks used for extracting stock market data as part of the Azure Data Factory (ADF) pipeline. These notebooks act as the **core processing layer**, handling API ingestion, validation, and storage using PySpark and Delta Lake.

---

This notebook is responsible for fetching **daily stock price data** from an external stock market API and preparing it for further processing.

**Key Responsibilities:**
- Ingests daily stock data using API requests
- Parses and normalizes raw JSON responses
- Applies basic data quality checks and schema validation
- Converts data into Spark DataFrames
- Writes data to **Delta tables** in the Bronze layer

**Technologies Used:**
- PySpark
- Databricks
- Delta Lake
- Azure Data Lake Storage (ADLS Gen2)

📸 **Notebook Screenshot:**
1. Daily Ingestion
<img width="1920" height="4880" alt="Daily-Databricks" src="https://github.com/user-attachments/assets/5c204c69-d27c-423a-aeb5-f908b05c82e5" />

2. Historical Data Ingestion
<img width="1920" height="5191" alt="Historical-Databricks" src="https://github.com/user-attachments/assets/ff6cc1cb-1f0b-4e5c-a193-75203e94755b" />

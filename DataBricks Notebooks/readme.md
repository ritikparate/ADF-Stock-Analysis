# 📓 Databricks – Stock Data Extraction Notebooks

This section documents the Databricks notebooks used for extracting stock market data as part of the Azure Data Factory (ADF) pipeline. These notebooks act as the **core processing layer**, handling API ingestion, validation, and storage using PySpark and Delta Lake.

---

This notebook is responsible for fetching **daily stock price data** from an external stock market API and preparing it for further processing.

## **Key Responsibilities:**
- Ingests daily stock data using API requests
- Parses and normalizes raw JSON responses
- Applies basic data quality checks and schema validation
- Converts data into Spark DataFrames
- Writes data to **Delta tables** in the Bronze layer

## **Technologies Used:**
- PySpark
- Databricks
- Delta Lake
- Azure Data Lake Storage (ADLS Gen2)

## 📸 **Notebook Screenshot:**
<table>
  <tr>
    <th>Daily Stock Ingestion</th>
    <th>Historical Stock Ingestion</th>
  </tr>
  <tr>
    <td>
      <img src="https://github.com/user-attachments/assets/9ff03b9f-206e-4b2b-8ae6-7aeb106bf105"  width="100%">
    </td>
    <td>
      <img src="https://github.com/user-attachments/assets/1883490c-40c2-42a3-9222-edbdb9e16f04" width="100%">
    </td>
  </tr>
</table>

# Stock Analysis with Azure Data Factory

## Overview
This project implements an end-to-end data pipeline on Azure to fetch, process, and analyze stock market data using the Medallion Architecture (Bronze → Silver → Gold). The pipeline handles both real-time daily stock data and historical data spanning 10 years, making it ready for analytics and business intelligence.
Built this to get hands dirty with Azure's data engineering stack while transitioning from support engineering. The goal was simple: automate stock data collection and make it analysis-ready.

## Problem Statement
Financial analysts and traders need clean, structured stock data for decision-making. Manually collecting and processing this data from multiple sources is time-consuming and error-prone. This pipeline automates the entire workflow—from data extraction to creating analysis-ready datasets.

### High-Level Data Flow
```
yfinance API → Azure Databricks → ADLS Gen2 (Bronze) → ADF (Processing) → ADLS Gen2 (Silver) → ADF Data Flows (Transformations) → ADLS Gen2 (Gold)
```

### Components
- **Data Source**: yfinance API (Python library for Yahoo Finance data)
- **Ingestion Layer**: Azure Databricks notebooks
- **Storage**: Azure Data Lake Storage Gen2
- **Orchestration**: Azure Data Factory pipelines
- **Transformation**: ADF Data Flows
- **Triggers**: Azure DataFactory Triggers
- **Monitoring**: Azure Monitor with email alerts

## Pipeline Architecture

### 1. Daily Data Pipeline
**Trigger**: Runs every 2 minutes  
**Purpose**: Capture intraday stock movements
<img width="900" height="300" alt="image" src="https://github.com/user-attachments/assets/333fbacd-ba7e-4e52-b7eb-4bc6fe069e3e" />

**Flow**:
1. **Databricks Notebook** fetches current-day data from yfinance
2. Raw data lands in **Bronze Layer** (ADLS Gen2)
3. **Get Metadata activity** retrieves folder names for processing
4. **ForEach loop** processes each stock ticker individually
5. **Copy Data activities** separate stocks into individual containers
6. **Data Flow** applies transformations:
   - Date formatting and standardization
   - Aggregations (avg, sum, count, range calculations)
   - Business logic filters
   - Custom flags for trading signals
7. Transformed data written to **Silver Layer**
8. Final curated datasets stored in **Gold Layer**
9. Complete dataset backup maintained in **Final Sink** container

### 2. Historical Data Pipeline
**Trigger**: Runs weekly  
**Purpose**: Load 10 years of historical data for trend analysis
<img width="900" height="300" alt="image" src="https://github.com/user-attachments/assets/38be767e-2143-4c21-90f9-7a0215a931ff" />

**Flow**:
1. **Databricks Notebook** fetches 10-year historical data
2. Data stored in Bronze Layer by ticker
3. **Get Metadata** retrieves all historical folders
4. **ForEach + Copy Data** activities process each ticker
5. **Data Flow** performs transformations (similar to daily pipeline)
6. Direct sink to **Gold Layer** (no intermediate backups for historical data)

## Technical Implementation

### Data Layers Explained

**Bronze Layer (Raw)**
- Unprocessed data exactly as received from yfinance
- Removed some unnecessary columns
- Partitioned by date and ticker symbol
- Serves as source of truth for reprocessing

**Silver Layer (Cleansed)**
- Cleaned and validated data
- Added columns like, Price change, Price Range for day, Volume, etc.
- Applied transformations:
  - Missing value handling
  - Date type conversions
  - Column renaming for consistency
  - Outlier detection and flagging

**Gold Layer (Business-Ready)**
- Aggregated metrics per stock
- Calculated fields: moving averages, price ranges, volume trends
- Optimized for downstream analytics
- Ready for consumption by BI tools or ML models

### Key Transformations in Data Flows

From the screenshots, here's what happens in the Silver transformation:

**1. Daily Data Transformations**
<img width="900" height="350" alt="image" src="https://github.com/user-attachments/assets/fc546afe-1fab-4b84-a884-4ffc57be9369" />

**2. Historical Data Transformations**
<img width="900" height="350" alt="image" src="https://github.com/user-attachments/assets/e0a48b2a-6624-45a0-bdb3-91c202a4447c" />


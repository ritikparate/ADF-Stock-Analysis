# Stock Analysis with Azure Data Factory

## Overview
This project implements an end-to-end data pipeline on Azure to fetch, process, and analyze stock market data using the Medallion Architecture (Bronze → Silver → Gold). The pipeline handles both real-time daily stock data and historical data spanning 10 years, making it ready for analytics and business intelligence.
Built this to get hands dirty with Azure's data engineering stack while transitioning from support engineering. The goal was simple: automate stock data collection and make it analysis-ready.

## Problem Statement
Financial analysts and traders need clean, structured stock data for decision-making. Manually collecting and processing this data from multiple sources is time-consuming and error-prone. This pipeline automates the entire workflow—from data extraction to creating analysis-ready datasets.

## Project Structure
```
stock-analysis-adf/
├── databricks-notebooks/
│   ├── daily_stock_fetch.ipynb
│   └── historical_stock_fetch.ipynb
├── adf-pipelines/
│   ├── Daily_Data_Pipeline.json
│   └── Historical_Data_Pipeline.json
├── data-flows/
│   ├── Daily_Silver_Transformation.json
│   └── History_Silver_Transformation.json
├── adls-structure/
│   ├── bronze/
│   ├── silver/
│   ├── gold/
└──   └── final-sink/
```

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

## Tech Stack
- **Cloud Platform**: Microsoft Azure
- **Orchestration**: Azure Data Factory
- **Compute**: Azure Databricks (Python notebooks)
- **Storage**: Azure Data Lake Storage Gen2
- **Data Processing**: ADF Data Flows (Spark-based)
- **Monitoring**: Azure Monitor
- **Programming**: Python and Pyspark (yfinance, pandas)

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
<img width="1200" height="600" alt="image" src="https://github.com/user-attachments/assets/fc546afe-1fab-4b84-a884-4ffc57be9369" />

**2. Historical Data Transformations**
<img width="1200" height="600" alt="image" src="https://github.com/user-attachments/assets/e0a48b2a-6624-45a0-bdb3-91c202a4447c" />

For details:
1. **Union operations** combine data from multiple stock sources
2. **Aggregate functions** calculate:
   - Average prices (open, close, high, low)
   - Total trading volumes
   - Daily price ranges
   - Count of trading days
3. **Derived columns** add:
   - Price change percentages
   - Sector classifications
   - Volatility indicators
   - Price movement thresholds
4. **Seperate Sink(Gold and Aggregated) in single Data Flow**

### Stocks Tracked
Based on the data flow screenshot, the pipeline processes major stocks including:
- Tesla (TSLA)
- Nvidia (NVDA)
- Apple (AAPL)
- Moderna (MRNA)
- Oriental Culture Holding (OCG)

Each stock gets its own transformation path in the data flow for parallel processing.

## Monitoring & Reliability

### Alert Configuration
- **Alert Rule**: "Daily Alert" monitors pipeline failures
- **Severity**: Sev0 (highest priority)
- **Trigger Condition**: Whenever Pipeline Failed Runs > 0
- **Notification**: Email + Push notifications to action group
- **Evaluation**: Monthly rate monitoring
  <img width="1200" height="600" alt="image" src="https://github.com/user-attachments/assets/eab1dd6d-9ac9-40f6-9fa5-c95a61dc447a" />

### Success Metrics
From the pipeline runs shown:
- **Success Rate**: 100% (all activities showing green checkmarks)
- **Average Duration**: 
  - Data flows: 1-2 minutes
  - Copy activities: 14-16 seconds
  - Notebook execution: 11 minutes (with starting cluster for first time), Otherwise 1-2 minutes
<img width="900" height="400" alt="image" src="https://github.com/user-attachments/assets/65ee14dd-4ba6-457d-a995-9dea4f203abb" />

## Key Learnings
- **Medallion Architecture** isn't just theory—it genuinely makes debugging easier when you can trace back through layers
- **ForEach activities** are powerful but need careful tuning. Parallel execution can backfire without proper resource allocation
- **Data Flows** are great for complex transformations but have a learning curve. Started with copy activities, graduated to flows for advanced logic
- **Monitoring is non-negotiable**—the alert system saved me multiple times when APIs went down overnight
- **Incremental loads** should have been implemented from day one. Learned this the hard way after processing full historical data repeatedly

<u><strong>It's not the most complex pipeline out there, but it's mine, and I can explain every decision made.</strong></u>

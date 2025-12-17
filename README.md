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

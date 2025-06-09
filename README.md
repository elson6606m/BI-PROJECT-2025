# 📊 Stock Market ELT Project — Final BI Assignment
## 🎓 MADSC301 - Business Intelligence
* *Student Project (Spring Semester 2025)*
* *Instructor: Prof. Hachem*
* *Submitted by: Elson Shaji*
## Overview
This project demonstrates a complete ETL/ELT pipeline for stock market data analysis, using real-time API data, cloud-based data warehousing, automation, and BI dashboarding. The workflow was designed to simulate a real-world data engineering scenario and fully meets the MADSC301 final assignment objectives.

## 🚀 Project Architecture
        +-----------------+
        | Polygon.io API  |
        +-----------------+
               |
           [Extract]
               |
               v
      +------------------+          +--------------------+
      | Python + Pandas  |          | .env for secrets   |
      +------------------+          +--------------------+
               |
           [Transform / Clean]
               |
               v
      +------------------+
      | BigQuery (GCP)   |
      +------------------+
               |
           [dbt Transform]
               |
               v
      +------------------+
      | BI Dashboard     |
      | (Looker Studio)  |
      +------------------+
               |
           [Scheduled Run]
               |
               v
         +------------+
         | Prefect.io |
         +------------+


## 🧠 Business Case
The goal of this pipeline is to analyze real-time stock price trends, trading volume, and moving averages across multiple tickers. This analysis supports investment decisions, price forecasting, and technical analysis using a visual dashboard.
# ✅ Project Components
## 1. 📥 Data Collection
* *Source:* *[Polygon.io](https://polygon.io/)*
* *Method:* API call to aggregates endpoint (daily OHLCV for multiple stock symbols)
* *Format:* .csv and .xlsx
* *API Key:* Managed securely using .env
## 2. 🧹 Data Cleaning & Preparation
* *Used Pandas to:*
   * *Handle missing values*
   * *Normalize date format*
   * *Rename ambiguous columns*
   * *Filter stock tickers and columns*
   * *Converted Excel to clean DataFrame for upload*

## 3. 🗄 Data Storage
* *Warehouse: Google BigQuery (GCP)*
* *Staging Table:* multiple_stocks
* *Transformed Table:* my_first_dbt_model
* *Credentials:* Handled via GCP Service Account Key (.json file)
Use the package manager [pip](https://pip.pypa.io/en/stable/) to install foobar.

## 4. Workflow Orchestration
* *Tool Used: Prefect* 
* *ETL Flow Script: etl_flow.py*
   * *extract.py: Downloads and saves raw data*
   * *load.py: Uploads to BigQuery*
   * *dbt run: Transforms using SQL models*
* *Execution: python etl_flow.py*

## 5. 📊 Analysis & Visualization
* *Tool: Looker Studio (Google Data Studio)*
* *Visuals:*
   * *Line Chart (Closing price over time)*
   * *Bar Chart (Daily trading volume)*
   * *Table View (Raw data inspection)*
   * *Combo Chart (High vs. Low)*
   * *Scatter Plot (Close vs Volume)*
   * *Calculated fields for Moving Averages*

## 6. ⚙ Scheduling & Automation

To automate the end-to-end ETL pipeline (Extract → Load → Transform), we used Prefect, a modern workflow orchestration tool. This allows the pipeline to run automatically without manual intervention.

### 🔄 Workflow Overview

The automated pipeline includes the following steps:

1. Extract:
   Calls get_multiple_stocks_with_delay.py to fetch OHLCV data from the Polygon API.

2. Load:
   Executes load.py to load the cleaned data into a BigQuery table.

3. Transform:
   Triggers dbt run to perform data transformation and modeling within BigQuery.

---

### 🧠 Tool Used: [Prefect](https://www.prefect.io/)

Prefect was used in local execution mode (no Prefect Cloud account needed), which suits standalone or development environments.

#### ✅ Automation Setup Steps

1. Installed Prefect into the Python virtual environment:

   bash
   pip install prefect
   

2. Created a Flow using etl_flow.py, which defines the sequence of tasks:

   python
   from prefect import flow, task
   import subprocess

   @task
   def extract():
       subprocess.run(["python", "get_multiple_stocks_with_delay.py"], check=True)

   @task
   def load():
       subprocess.run(["python", "load.py"], check=True)

   @task
   def transform():
       subprocess.run(["dbt", "run"], cwd="dbt/my_dbt_project", check=True)

   @flow(name="Stock Market ETL")
   def etl_pipeline():
       extract()
       load()
       transform()
   

3. Built a Deployment (used for scheduling):

   bash
   prefect deployment build etl_flow.py:etl_pipeline -n "daily-stock-job" 

  

4. *Started a Local Worker* to listen for flow runs:

   bash
   prefect worker start --pool 'default'
   

5. Manually triggered the scheduled run (or could be triggered by time in future):

   ```bash
   prefect deployment run 'Stock Market ETL/daily-stock-job'
  

### ✅ Why Prefect?

* No need for cloud dependencies (runs locally)
* Modular task management
* Easy to retry, reschedule, or rerun failed flows
* Extendable to Prefect Cloud for production


## 7. ✅ Extras / Bonus
 * *.env file for secure key management*
 * *Virtual Environment (venv)*
 * *requirements.txt included*
 * *Prefect orchestration*

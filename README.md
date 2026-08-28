# 🚕 NYC Taxi End-to-End Analytics Platform (Microsoft Fabric)

## 📌 Project Overview
This project demonstrates an end-to-end Data Engineering and Business Intelligence solution using **Microsoft Fabric**. It processes NYC Taxi transactional data, moves it through **Landing**, **Staging**, and **Presentation** layers within the Synapse Data Warehouse using T-SQL and Data Factory, and presents executive KPIs via an interactive Power BI report.

---

## 🏗️ Data Architecture & Pipeline

<img width="1692" height="929" alt="ChatGPT Image Aug 28, 2026, 09_30_22 PM" src="https://github.com/user-attachments/assets/66e421af-cc02-48cf-b2ae-e4f55e05a754" />


The data pipeline consists of 4 main stages:

1. **Landing:** Raw `Parquet` files ingested from NYC Taxi & Limousine Commission.
2. **Staging:** Data loaded into `stg.nyctaxi_yellow` and `stg.taxi_zone_lookup` via **Data Factory Copy Activity**.
3. **Presentation:** Data transformed, cleaned, and optimized into `dbo.nyctaxi_yellow` using **Dataflow & T-SQL Stored Procedures**.
4. **Reporting:** Direct connection to Power BI Semantic Model for data visualization.

---

## 🛠️ Tech Stack & Tools
- **Platform:** Microsoft Fabric
- **Storage & Warehouse:** Synapse Data Warehouse
- **Orchestration & ETL:** Fabric Data Factory, Copy Activity, Dataflow, T-SQL Stored Procedures
- **Data Modeling & Visualization:** Power BI

---

## 📊 Dashboard Preview

<img width="1356" height="768" alt="Gemini_Generated_Image_p8j5qqp8j5qqp8j5 (1)" src="https://github.com/user-attachments/assets/1682f675-3ef5-46ec-964c-0dda93f52b93" />


### Key Insights Tracked:
- **Total Revenue & Completed Trips KPIs**
- **Daily Revenue Trends**
- **Payment Method Distribution**
- **Pickup Density & Trip Distribution by Borough**

---

## 📁 Repository Structure
- `docs/`
        |_pl_stg_processing_nyctaxi.md
        |_pl_pres_processing_nyctaxi.md
        |_pl_orchestration_nyctaxi.md


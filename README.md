# 🌋 Italy Earthquakes: Dimensional Data Warehouse

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![ETL](https://img.shields.io/badge/Pipeline-ETL-orange)
![GIS](https://img.shields.io/badge/GIS-GeoPandas-green)
![Status](https://img.shields.io/badge/Status-Complete-success)

A comprehensive Data Engineering project that builds a **Dimensional Data Warehouse** for analyzing seismic activity in Italy. The system fetches raw telemetry from USGS, enriches it with geological fault data, and structures it into a **Star Schema** optimized for OLAP analysis and swarm detection.

<img width="100%" alt="Star Schema" src="https://github.com/user-attachments/assets/84e1ddb6-d67f-477e-b27d-abe966361239" />
*(Figure: Tableau Dashboard)*

## 📖 Project Overview
The goal of this project was to move beyond simple data visualization and build a robust data model capable of answering complex geological questions.
It implements a full **ETL (Extract, Transform, Load)** pipeline:
1.  **Extracts** global seismic data via the **USGS FDSN API**.
2.  **Transforms** raw CSVs into structured dimensions, performing data cleaning and quality assessment (validity, completeness).
3.  **Integrates** external geospatial datasets (Active Faults & Administrative Boundaries).
4.  **Loads** data into a **Star Schema** design for high-performance querying.

## ⚙️ The ETL Pipeline

### 1. Data Acquisition & Cleaning
* **Source:** United States Geological Survey (USGS) API.
* **Strategy:** Batched API requests to handle query limits and strict geographical filtering to isolate Italian events.
* **Quality Check:** Automated assessment of `gap`, `rms`, and `depth` attributes to ensure data validity before loading.

### 2. Geospatial Integration (Enrichment)
The raw data lacked context. I implemented **Spatial Joins** using `GeoPandas` to enrich every earthquake record with:
* **Administrative Data:** Mapped coordinates to specific **Countries** and **Regions** using *Natural Earth* vector data.
* **Geological Context:** Mapped events to the nearest **Capable Fault** using the **ITHACA** (ITaly HAzard from CApable faults) database.
    * *Impact:* This allows queries like *"Show average magnitude of earthquakes triggered by the Mt. Etna fault system."*

### 3. Dimensional Modeling (Star Schema)
The database was modeled using a **Star Schema** architecture containing:
* **Fact Table:** `Earthquake` (Transactional granularity, one row per event).
* **Dimension Tables:**
    * `Dim_Time`: Hierarchical drill-down (Year -> Quarter -> Month -> Day -> Hour).
    * `Dim_Location`: Geography hierarchy.
    * `Dim_Fault`: Geological classification.
    * `Dim_Type`: Magnitude type and measurement method.

## 🔬 Advanced Analytics: Swarm Detection
A key feature is the **Materialized View for Swarm Analysis**.
Detecting seismic "swarms" (clusters of tremors) requires computationally expensive spatio-temporal self-joins. To optimize this, I implemented a materialized view logic with configurable parameters:

* **Mainshock Definition:** Magnitude threshold (e.g., $M \ge 3.0$).
* **Swarm Window:** Time and distance look-back window (e.g., events within **29 days** prior and **500 km** radius).
* **Result:** A pre-calculated table linking Mainshocks to their precursor swarms, enabling instant analysis without running heavy queries at runtime.

## 🛠️ Tech Stack
* **Language:** Python
* **Data Manipulation:** Pandas, NumPy
* **Geospatial Analysis:** GeoPandas, Shapely, Reverse Geocoder
* **Architecture:** Star Schema, Dimensional Modeling

## 👤 Author
**Daniel Curcio**

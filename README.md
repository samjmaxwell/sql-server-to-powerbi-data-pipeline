# MySQL-to-Power-BI Business Intelligence Pipeline

## Overview

This portfolio project demonstrates an end-to-end business intelligence workflow for a fictional retail company, **ComfyCo**. It takes operational data through extraction, MySQL staging and transformation, dimensional modeling, and interactive Power BI reporting.

The project answers practical business questions:

- How do revenue, sales volume, and profitability change over time?
- Which regions, channels, customer segments, and products drive performance?
- How does actual performance compare with plans and targets?
- Which inventory, headcount, capacity, procurement, and cost factors support or constrain sales?

## Why This Project Matters

A dashboard is only as reliable as the data and model behind it. This project separates raw-source handling from analytical transformations so reporting logic is traceable, reusable, and easier to maintain.

It demonstrates:

- **ETL thinking:** source data is cleaned before it reaches the reporting layer.
- **Dimensional modeling:** facts and dimensions are separated to support consistent filtering and aggregation.
- **Analytical enablement:** reusable SQL views and Power BI relationships make KPIs easier to build and validate.
- **Business communication:** the report translates operational metrics into executive and planning views.

## Architecture

```text
Excel / CSV operational sources
            |
            v
Raw source tables in MySQL
            |
            v
SQL staging and transformation
(actual_* scripts -> clean_* scripts)
            |
            v
Dimensions, join keys, and analytical tables/views
            |
            v
Power BI relationships and DAX measures
            |
            v
Interactive decision-support dashboard
```

Cleaning and standardizing data in MySQL creates a stable reporting dataset and keeps transformation rules outside report visuals. Power BI then handles semantic modeling, relationships, DAX calculations, and presentation.

## Data Model

The project follows an analytical star-schema pattern:

- **Sales fact:** `clean_salesdb` contains the central transactional dataset with order, revenue, units, product, channel, region, and customer information.
- **Dimensions:** SQL views create region, product category, product name, customer segment, and channel datasets.
- **Planning and operations:** sales targets, inventory, procurement, demand, returns, COGS, OpEx, and headcount/capacity provide supporting analytical data.
- **Join keys:** composite identifiers such as `SalesChannelID`, `SalesRepJoinID`, and `ProdNameYMID` support relationships across reporting grains.
- **Time analysis:** year, quarter, and month fields enable time-based filtering and trends.

> Every relationship should preserve the correct grain. Dimension keys should be unique, while fact rows may repeat the same dimension key many times.

## Dashboard Preview

![ComfyCo Power BI report example](powerbi-model/Comfy%20Co%20Report%20-%20PBI%20Report%20Example.png)

| Dashboard area | Primary purpose |
|---|---|
| Executive summary | Quickly understand sales performance and key KPIs |
| Customer trends | Explore segmentation and customer behavior |
| Channel mix | Compare revenue and performance across sales channels |
| Regional trends | Drill into province and region-level differences |
| Sales and operations planning | Connect commercial targets with operational planning |

## Technology

| Tool | Role |
|---|---|
| **Excel** | Initial operational source format and data structure |
| **MySQL** | Data storage, cleaning, SQL transformations, views, keys, and analytical tables |
| **Power BI** | Semantic model, relationships, DAX, and interactive visualization |
| **SQL** | Reusable data transformation and dimensional-modeling logic |

## Repository Structure

```text
.
├── sql-scripts/
│   ├── actual_*                 # Source loading and initial cleaning patterns
│   └── clean_*                  # Transformed tables, views, and join keys
├── powerbi-model/
│   └── Comfy Co Report - PBI Report Example.png
├── dashboard-images/            # Reserved for exported dashboard visuals
├── video-walkthrough/           # Reserved for project demonstration media
└── README.md
```

## Reproduce the Workflow

### Prerequisites

- MySQL Server 8.x or later
- MySQL Workbench or another SQL client
- Power BI Desktop on Windows
- Access to the source workbooks or CSV files

### 1. Create the database

```sql
CREATE DATABASE comfyco_salesdb;
USE comfyco_salesdb;
```

### 2. Load source data

Load each operational workbook or CSV source into MySQL using MySQL Workbench import, a bulk-load process, or a repeatable ingestion script. Source tables should use names such as `salesdb`, `headcount_capacity_db`, `inventory_db`, and `sales targets`.

> Best practice: keep raw source tables unchanged and build cleaned tables or views from them. This creates an auditable boundary between source data and transformations.

### 3. Run SQL transformations

Start with the `actual_*` scripts to create or transform cleaned tables, then run the matching `clean_*` scripts to create dimensions, join keys, and reporting structures.

Example ordering:

```sql
SOURCE sql-scripts/actual_salesdb.sql;
SOURCE sql-scripts/clean_salesdb.sql;
SOURCE sql-scripts/actual_salestargets.sql;
SOURCE sql-scripts/clean_salestargets.sql;
```

Repeat the pattern for the operational datasets included in `sql-scripts/`.

### 4. Validate before modeling

```sql
SELECT COUNT(*) AS sales_rows
FROM clean_salesdb;

SELECT COUNT(*) AS null_regions
FROM clean_salesdb
WHERE Region IS NULL;

SELECT COUNT(*) AS duplicate_sales_rows
FROM clean_salesdb
GROUP BY Order_ID
HAVING COUNT(*) > 1;
```

Before visualizing data, verify row grain, null values, duplicate keys, orphan relationships, and date coverage.

### 5. Connect Power BI

1. Open Power BI Desktop.
2. Select **Get Data → MySQL Database**.
3. Enter:

```text
Server: localhost:3306
Database: comfyco_salesdb
```

4. Import `clean_*` tables and SQL views.
5. Create one-to-many relationships from unique dimension keys to fact keys.
6. Mark the calendar table as a date table if one is included.
7. Build DAX measures at the correct grain rather than embedding calculations in individual visuals.

## Data Quality and Modeling Decisions

- **Preserve raw data:** transformations should not overwrite source records.
- **Standardize identifiers:** normalize prefixes or punctuation consistently.
- **Trim text:** remove accidental whitespace and inconsistent category labels.
- **Define the grain:** every fact table should clearly identify what one row represents.
- **Avoid many-to-many relationships:** use bridge tables or dimensions where entities can repeat.
- **Validate before visualizing:** check nulls, duplicates, orphan keys, and date coverage.

## Skills Demonstrated

- SQL-based data cleaning and transformation
- Star-schema and dimensional data modeling
- Fact/dimension relationship design
- Business KPI development
- Power BI report modeling and visualization
- Cross-functional sales and operations analysis
- Data quality validation and documentation

## Known Improvement Opportunities

- Add a controlled and repeatable source-loading process.
- Introduce explicit `raw`, `staging`, and `analytics` schemas.
- Add incremental-load dates and audit columns.
- Enforce primary and foreign-key constraints where supported by the source grain.
- Export and commit a Power BI `.pbix` file.
- Publish a data dictionary containing KPI definitions and table grains.
- Add SQL tests for nulls, duplicates, and referential integrity.
- Document refresh schedules and data lineage.

## Author

**Samuel John-Maxwell**  
Charlottetown, PE, Canada  
Data Analytics and Process Improvement Professional

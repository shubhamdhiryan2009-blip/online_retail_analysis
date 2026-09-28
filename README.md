🛍️ Online Retail Data Warehouse & Sales Analytics
A comprehensive end-to-end SQL and Data Analytics project based on transactional data from a UK-based online retailer. This repository covers the complete ELT (Extract, Load, Transform) pipeline—from raw CSV staging and silver-layer data cleaning/outlier filtering using Interquartile Range (IQR) to business metrics reporting.

📌 Project OverviewThis project builds a structured Data Warehouse layer (silver_online_retails_sales) from raw transactional data (online_retail.csv) containing 541,909 sales records. The primary objective is to clean raw transactional anomalies, standardize product codes, remove extreme quantity outliers using statistical bounds, and deliver high-value business insights on revenue, customer RFM behavior, and order cancellation trends.   

📁 Repository Structure
.
├── online_retail.csv        # Raw dataset containing 541,909 retail transaction records
├── DDL_SILVER_DATA.sql      # SQL script containing Staging, Silver ETL & Analytical Queries
└── README.md                # Project documentation

🏗️ Data Architecture & ELT Pipeline1. 
1. Raw Staging Layer (online_retails_staging)Raw transactional data is loaded directly from online_retail.csv into a SQL Server staging table with relaxed string types using BULK INSERT:   InvoiceNo / StockCode / Description: Character data fields.   Quantity / UnitPrice / CustomerID: Staging strings for safe ingestion.
2. Silver Data Layer (silver_online_retails_sales)Data from staging undergoes transformation, feature engineering, and cleaning:
   Invoice Handling: Extracted pure numeric invoice_no and engineered cancel_invoice bit flag for standard and cancelled orders (prefixed with 'C').   StockCode Categorization: Categorized item types into 'SERVICE', 'GIFT_VOUCHER', or standard 'PRODUCT' based on prefix/code patterns.
   Outlier Removal (IQR Strategy): Computed $Q_1$ (25th percentile) and $Q_3$ (75th percentile) per Base_StockCode across positive sales volumes. Applied $[Q_1 - 1.5 \times \text{IQR}, Q_3 + 1.5 \times \text{IQR}]$ bounds to filter out quantity anomalies.   Calculated Revenue: $[ \text{Quantity} \times \text{UnitPrice} ]$ computed dynamically at the row level.

| Column Name      | Data Type       | Description                | Transformation Logic                                        |
| ---------------- | --------------- | -------------------------- | ----------------------------------------------------------- |
| `invoice_no`     | `VARCHAR(50)`   | Cleaned Invoice Identifier | Substring extracted to strip the `'C'` prefix               |
| `cancel_invoice` | `BIT`           | Cancellation Status        | `1` if order is cancelled, `0` otherwise                    |
| `StockCode_Type` | `VARCHAR(50)`   | Product Classification     | Classified as `'SERVICE'`, `'GIFT_VOUCHER'`, or `'PRODUCT'` |
| `Base_StockCode` | `VARCHAR(50)`   | Standardized Stock Code    | Truncated to the base 5-digit product code                  |
| `Description`    | `VARCHAR(1000)` | Item Description           | Converted to uppercase and trimmed                          |
| `Quantity`       | `INT`           | Order Volume               | Converted to integer and filtered using IQR                 |
| `InvoiceDate`    | `DATETIME`      | Transaction Timestamp      | Cast to `DATETIME`                                          |
| `UnitPrice`      | `DECIMAL(10,2)` | Price per Item             | Cast to `DECIMAL(10,2)`                                     |
| `CustomerID`     | `VARCHAR(50)`   | Unique Customer ID         | Identifies individual retail buyers                         |
| `Country`        | `VARCHAR(50)`   | Country of Purchase        | Used for geographic classification                          |
| `Revenue`        | `DECIMAL(10,2)` | Total Line Revenue         | Calculated as `Quantity × UnitPrice`                        |


📈 Key Analytical Use Cases & SQL Queries
The repository includes analytical queries (DDL_SILVER_DATA.sql) addressing core retail KPI analysis:   
1. Monthly Revenue & Growth Trends: Monthly performance tracking and revenue ranking across calendar years.
2. Revenue Share by Product Category: Percentage contribution of physical products vs. services/gift vouchers.
3. Top Best-Selling Products: Identifies top 10 products by gross revenue generation.
4. Geographic Performance & Average Order Value (AOV): Country-level total revenue and calculated Average Order Value ($\text{Total Revenue} / \text{Invoice Count}$).
5. Cancellation Rate & Clustering Analysis: Item and country breakdown of cancelled transactions.
6. Customer Value & RFM Segmentation: Monthly transactional activity and total spend per customer.
  
🛠️ Setup & Execution Instructions
Prerequisites
Microsoft SQL Server / Azure SQL Database 
Local access to online_retail.csv

Steps 
1. Prepare Data Staging: Ensure online_retail.csv is stored on your machine or accessible path.
2. Execute DDL Script: Run DDL_SILVER_DATA.sql in SQL Server Management Studio (SSMS) or Azure Data Studio:
   Executes staging table creation & bulk loading.
   Creates silver data schema and executes cleaning CTES with IQR bounds.
   Populates silver_online_retails_sales.
3. Run Business Queries: Execute the analytical section of the SQL script to generate business performance metrics.   

  📝 LicenseThis project is licensed under the MIT License - see the LICENSE file for details.

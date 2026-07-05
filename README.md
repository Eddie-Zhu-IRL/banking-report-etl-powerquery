# banking-report-etl-powerquery
Excel Power Query ETL pipeline that imports, transforms, and standardizes multiple simulated banking txt-based daily reports into structured datasets for reporting and analysis.


## 📊 Banking Report ETL Pipeline (Excel Power Query)
### 📌 1.Overview

This project is a Power Query-based ETL pipeline designed to simulate a real-world banking reporting system. It processes multiple structured txt-based reports, transforms them into clean datasets, and prepares them for analysis in Excel.

The solution demonstrates how Excel can be used as a lightweight data engineering tool for batch report processing.

**⚠️ Data Disclaimer**

All the seven txt-based datasets in this project are synthetically generated banking-style reports used for testing and educational purposes. No real financial or customer data is included.

### 🏗️ 2. Architecture

Raw TXT Files (Folder) <br>
        ↓       
Power Query Folder Connector <br>
        ↓        
File Inventory Table (Metadata Layer) <br>
        ↓        
Report-Specific Transformations <br>
        ↓        
Structured Excel Tables


### 📁 3. Input Data

The pipeline processes the following simulated banking reports:

  (1) Customer Loan Portfolio Report
  (2) Loan Repayment Due Report
  (3) Delinquent Loan Report
  (4) Mortgage Portfolio Report
  (5) Loan Disbursement Report
  (6) Customer Account Summary Report
  (7) Daily Loan Transaction Report
  
### ⚙️ 4. Key Features
Folder-based dynamic file ingestion using Power Query
Parameterized file path for flexible daily data switching
Automated parsing of semi-structured TXT reports
Standardization of:
Dates
Numeric values
Column naming conventions
Separation of metadata (headers/footers) from transactional data
Fully refreshable ETL pipeline (single-click refresh)
🔄 Workflow
New daily reports are downloaded into a folder
Folder path is set via a Power Query parameter
Excel “Refresh All” triggers ingestion and transformation
All datasets update automatically
📊 Output

Clean, structured Excel tables ready for:

PivotTables
Dashboards
Financial reporting
Power BI integration
Data analysis
🛠️ Tools Used
Microsoft Excel
Power Query (M Language)
ETL Design Principles
Data Cleaning & Transformation
Folder-based Data Ingestion
🚀 Key Achievement

This project eliminates manual report processing by implementing an automated ETL workflow. The system enables scalable ingestion of multiple banking-style reports with minimal user intervention.

📸 Screenshots (Recommended)

Add images like:

Folder query view
Power Query steps
Final transformed tables
Refresh output
👨‍💻 Author

Your Name
Data Analyst / Aspiring Data Engineer

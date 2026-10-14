# Banking-Report-ETL-PowerQuery
Excel Power Query ETL pipeline that imports, transforms, and standardizes multiple simulated banking txt-based daily reports into structured datasets for reporting and further analysis.

## 📊 Banking Report ETL Pipeline (Excel Power Query)
### 📌 1. Overview

This project is a Power Query-based ETL pipeline designed to simulate a real-world banking reporting system. It processes multiple structured txt-based reports, transforms them into clean Excel tables, and prepares them for further analysis in Excel.

The solution demonstrates how Excel can be used as a lightweight data engineering tool for batch report processing. Key steps ate one parameter change and on Refresh All instead of regular manual Text Import Wizard steps for every report.

**⚠️ Data Disclaimer**

All the seven txt-based datasets in this project are synthetically generated banking-style reports used for testing purposes. They simulate similar format of reports data as I worked with. No real financial or customer data is included.

- **The Problem**
I worked in an operational admin team, downloaded seven daily reports from system and processed them using Excel for analysis. Each report was prepared in the following way,
     1. Open the txt-based file in Excel.
     2. Text Import Wizard,
        - step 1: choose Fixed width
        - step 2: click to place column break lines at the right character positions
        - step 3: set the data format of each column
        - Finish
     3. Delete the report header and footer by hand. Or re-format and/or calculate columns as required.

![](Screenshots/Manual_Steps.png)

The above steps were repeated for seven reports each business day by hand. The break lines and formats have to be set again each time and different staff placed the break lines and formatted columns in various ways, which lacked in consistency.

- **Why Power Query**<br>
The operations team has no access to SQL, Python or other data processing tools, and uses Excel in administrative level. Built into Excel, Power Query facilitates daily process of data import and manipulation by one parameter change and one click of Refresh All, with no coding needed to run the process.

- **The Solution**<br>
A pipeline by Power Query is created in one Excel workbook with following methods:
     - creates defined parameter **Path** to the folder for current working day, such as ~./14102026
     - reads each report from the day's folder -- 14102026
     - creates Target Rows Indicator for data rows, and drops header rows, footer rows and non-data rows
     - splits the fixed-width lines of data into columns based on position, trims the text, applies appropriate data types and renames columns
     - loads each report to its own sheet for further analysis

Comparison between Manual Steps and Power Query is listed in the following table,<br>
||Manual Steps (Before)|Power Query (After)|
|---|---|---|
| Per Report| Multiple Manual Steps in the wizard| None|
| Column Position and Types| Set Daily| Defined once in the Query|
| Header and Footer Removal| Delete by hand| Removed by rules|
| Daily Action| Repeat for 7 Reports| Change parameter "Path", then Refresh All|
  
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

```mermaid
graph LR;
A[Daily&nbsp;Folder:&nbsp;Path] --> B[TXT Reports];
A[Daily&nbsp;Folder:&nbsp;Path] --> C[Excel&nbsp;Workbook:&nbsp;Query&nbsp;Per&nbsp;Report];
C[Excel&nbsp;Workbook:&nbsp;Query&nbsp;Per&nbsp;Report] --> D[Read TXT];
C[Excel&nbsp;Workbook:&nbsp;Query&nbsp;Per&nbsp;Report] --> E[Target&nbsp;Data&nbsp;Rows];
C[Excel&nbsp;Workbook:&nbsp;Query&nbsp;Per&nbsp;Report] --> F[Keep&nbsp;Data&nbsp;Rows];
C[Excel&nbsp;Workbook:&nbsp;Query&nbsp;Per&nbsp;Report] --> G[Split Column];
C[Excel&nbsp;Workbook:&nbsp;Query&nbsp;Per&nbsp;Report] --> H[Trim,&nbsp;Format,&nbsp;Rename];
C[Excel&nbsp;Workbook:&nbsp;Query&nbsp;Per&nbsp;Report] --> I[Load&nbsp;to&nbsp;Excel&nbsp;Sheets]
```

### 📁 3. Input Data

The pipeline processes the following simulated banking reports. Attributes for each report, such as Data Rows, Output Columns and format of Columns are listed below,

| Report Name| Target Rows Indicator| Output Columns| Data Type: Format|
|---|---|---|---|
| Customer Account Summary Report| Starts With 001| Account_No, Account_Type, Currency, Balance, Status| Balance: Decimal Num|
| Customer Loan Portfolio Report| Starts With LN1| Loan_No, Customer_Name, Loan_Type, Balance, Interest, Status| Balance: Decimal Num, Interest: Percentage|
| Daily Loan Transaction Report| Starts With TXN5| Txn_ID, Loan_No, Customer_Name, Transaction, Amount| Amount: Decimal Num|
| Delinquent Loan Report| Starts With LN1| Loan_No, Customer_Name, Loan_Type, Balance, Days_Past_Due, Risk_Grade| Balance: Decimal Num|
| Loan Disbursement Report| Starts With LN2| Loan_No, Customer_Name, Product, Approved_Amount, Disbursed_Amount, Officer| Approved_Amount: Decimal Num, Disbursed_Amount: Decimal Num|
| Loan Repayment Due Report| Starts With LN1| Loan_No, Customer_Name, Due_Date, Installment, Days_Due, Status| Due_Date: Date, Installment: Decimal Num, Days_Due: Whole Num|
| Mortgage Portfolio Report| Starts With MTG| Mortgage_No, Borrower, Property_Value, Loan_Balance, LTV, Status| Property_Value: Decimal Num, Loan_Balance: Decimal Num, LTV: Percentage|

### ⚙️ 4. Key Features
  - **Dynamic File Connection**: connection is built on working folder using Power Query, txt. reports will be imported automatically once running Power Query
  - **Parameterized Folder**: each query reads txt report in folder from the same pre-defined **Path** parameter, so switching to a new day's working folder is one edit. 
  - **Rule-based Rows Identification**: each row is flagged by a Target Rows Indicator if row starts with a series of specific characters. Data will then be filtered based on this indicator. Team only needs data rows, so headers rows, footer rows and other irrelevant rows will be filtered out.
  - **Fixed-width Columns Processing**: Splitter.SplitTextByPositions separates single column into multiple columns based on positions, which performs same steps as column break lines does in Text Wizard.
  - **Standardization**: this includes text trimming, numeric formatting, date transforming, etc, for consistency when reports are processed by other staff.
    - Dates: eg, from 28-Jun-2026 to 01/06/2026 for future data aggregation
    - Numeric values: from text to whole number and/or decimal number
    - Column naming conventions
    - Separation of metadata (headers/footers) from transactional data: dynamically identify rows starting with characters of interest 
    - Refresh All to complete data import by updating parameter **Path**
    
### 🔄 5. Workflow
  - New daily reports are downloaded into a folder, eg 14102026
  - Folder path is set via Manage Parameters in Power Query.
  - In Excel, **Date -> "Refresh All”** to launch data import process
  - All reports are loaded into Excel sheets automatically.

     - 5.1 Final Outputs <br>
     
| Reports Name| Before Import| After Import|
|---|---|---|
| Customer Account Summary Report|||
| Customer Loan Portfolio Report|||
| Daily Loan Transaction Report|||
| Delinquent Loan Report|||
| Loan Disbursement Report|||
| Loan Repayment Due Report|||
| Mortgage Portfolio Report|![](Screenshots/raw_Mortgage_Portfolio_Report.png)|![](outputs/10_Mortgage_Portfolio_Report.png)|


### 📊 6. Output

Clean, structured Excel tables ready for:
  - PivotTables
  - Dashboards
  - Financial reporting
  - Power BI integration
  - Data analysis
    
### 🛠️ 7. Tools Used
  - Microsoft Excel
  - Power Query (M Language)
  - ETL Design Principles
  - Data Cleaning & Transformation
  - Folder-based Data Ingestion
    
### 🚀 8. Key Achievement

This project eliminates manual report processing by implementing an automated ETL workflow. The system enables scalable ingestion of multiple banking-style reports with minimal user intervention.

### 📸 9. Screenshots

  - **Data Folder View** <br>
![Data Folder](outputs/1_data_folder.png) 

  - **Parameter Setup** <br>
![Parameter Setup](outputs/2_Parameter_setup.png) 

  - **File Inventory** <br>
![File Inventory](outputs/3_output_Files_Inventory.png) 


**Final transformed tables**
  - Output Customer Account Summary Report <br>
![Output Customer Account Summary Report](outputs/4_Customer_Account_Summary_Report.png)

  - Output: Customer Loan Portfolio Report <br>
![Output: Customer Loan Portfolio Report](outputs/5_Customer_Loan_Portfolio_Report.png)

  - Output: Daily Loan Transaction Report <br>
![Output: Daily Loan Transaction Report](outputs/6_Daily_Loan_Transaction_Report.png)

  - Output: Delinquent Loan Report <br>
![Output: Delinquent Loan Report](outputs/7_Delinquent_Loan_Report.png)

  - Output: Loan Disbursement Report <br>
![Output: Loan Disbursement Report](outputs/8_Loan_Disbursement_Report.png)

  - Output: Loan Repayment Due Report <br>
![Output: Loan Repayment Due Report](outputs/9_Loan_Repayment_Due_Report.png)

  - Output: Mortgage Portfolio Report <br>
![Output: Mortgage Portfolio Report](outputs/10_Mortgage_Portfolio_Report.png)

**File Inventory Post Refreshing**
![File Inventory Post Refreshing](outputs/11_Files_Inventory_Post_Refreshing.png)

### 10. Power Query steps
![Power Queries Steps](outputs/12_Power_Queries_Steps.png)

👨‍💻 Author

**Eddie Zhu** <br>
Data Analyst / Aspiring Actuary

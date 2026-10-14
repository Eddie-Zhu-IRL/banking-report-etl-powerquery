# Banking-Report-ETL-PowerQuery
Excel Power Query ETL pipeline that imports, transforms, and standardizes multiple simulated banking txt-based daily reports into structured datasets for reporting and further analysis.

**⚠️ Data Disclaimer** <br>
All the seven txt-based datasets in this project are synthetically generated banking-style reports used for testing purposes. They simulate similar format of reports data as I worked with. No real financial or customer data is included.

## 📊 Banking Report ETL Pipeline (Excel Power Query)
### 📌 1. Overview

This project is a Power Query-based ETL pipeline designed to simulate a real-world banking reporting system. It processes multiple structured txt-based reports, transforms them into clean Excel tables, and prepares them for further analysis in Excel.

The solution demonstrates how Excel can be used as a lightweight data engineering tool for batch report processing. Key steps are one parameter change and one Refresh All instead of regular manual Text Import Wizard steps for every report.

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

```mermaid
flowchart LR;
A["Path Parameter<br>(the day's folder)"] --> B["Read Report .txt<br>by file name"];
B --> C["Flag Data Rows<br>(Target Rows Indicator)"];
C --> D["Keep Data Rows<br>(drop headers and footers)"];
D --> E["Split at fixed<br>Column Positions"];
E --> F["Trim, Set Types,<br>Rename Columns"];
F --> G["Load to Excel Sheet"];
A --> H["Data_Folder:<br>file inventory"];
```

### 📁 3. Input Data

The pipeline processes the following simulated banking reports. Attributes for each report, such as Data Rows, Output Columns and format of Columns are listed below,

| Report Name| Target Rows Indicator| Output Columns| Data Type: Format|
|---|---|---|---|
| Customer Account Summary Report| Starts With 001| Account_No, Account_Type, Currency, Balance, Status| Balance: Decimal Num|
| Customer Loan Portfolio Report| Starts With LN1| Loan_No, Customer_Name, Loan_Type, Balance, Interest, Status| Balance: Decimal Num, Interest: Percentage|
| Daily Loan Transaction Report| Starts With TXN5| Transaction_ID, Loan_No, Customer_Name, Transaction, Amount| Amount: Decimal Num|
| Delinquent Loan Report| Starts With LN1| Loan_No, Customer_Name, Loan_Type, Balance, Days_Past_Due, Risk_Grade| Balance: Decimal Num, Days_Past_Due: Whole Num|
| Loan Disbursement Report| Starts With LN2| Loan_No, Customer_Name, Product, Approved_Amount, Disbursed_Amount, Officer| Approved_Amount: Decimal Num, Disbursed_Amount: Decimal Num|
| Loan Repayment Due Report| Starts With LN1| Loan_No, Customer_Name, Due_Date, Installment, Days_Due, Status| Due_Date: Date, Installment: Decimal Num, Days_Due: Whole Num|
| Mortgage Portfolio Report| Starts With MTG| Mortgage_No, Borrower, Property_Value, Loan_Balance, LTV, Status| Property_Value: Decimal Num, Loan_Balance: Decimal Num, LTV: Percentage|

### ⚙️ 4. Key Features
  - **Parameterized Folder**: each query reads txt report in folder from the same pre-defined **Path** parameter, so switching to a new day's working folder is one edit. 
  - **Rule-based Rows Identification**: each row is flagged by a Target Rows Indicator if row starts with a series of specific characters. Data will then be filtered based on this indicator. Team only needs data rows, so headers rows, footer rows and other irrelevant rows will be filtered out.
  - **Fixed-width Columns Processing**: Splitter.SplitTextByPositions separates single column into multiple columns based on positions, which performs same steps as column break lines does in Text Wizard.
  - **Standardization**: this includes text trimming, numeric formatting, date transforming, etc, for consistency when reports are processed by other staff.
    - Dates: Due_Date changed to the start of month (eg. from 28-Jun-2026 to 01/06/2026) for monthly aggregation
    - Numeric values: from text to whole number and/or decimal number
    - Column naming conventions
    - Separation of metadata (headers/footers) from transactional data: dynamically identify rows starting with characters of interest 
    - Refresh All to complete data import by updating parameter **Path**
    
### 🔄 5. Workflow
  - New daily reports are downloaded into a folder, eg 14102026
  - Folder path is set via Manage Parameters in Power Query.
  - In Excel, **Data -> "Refresh All”** to launch data import process
  - All reports are loaded into Excel sheets automatically.

The M code of each query is in the [Queries](./Queries/) folder.

**5.1 Final Outputs**
     
| Reports Name| Before Import| After Import|
|---|---|---|
| Customer Account Summary Report|![](Screenshots/raw_Customer_Account_Summary_Report.png)|![](outputs/4_Customer_Account_Summary_Report.png)|
| Customer Loan Portfolio Report|![](Screenshots/raw_Customer_Loan_Portfolio_Report.png)|![](outputs/5_Customer_Loan_Portfolio_Report.png)|
| Daily Loan Transaction Report|![](Screenshots/raw_Daily_Loan_Transaction_Report.png)|![](outputs/6_Daily_Loan_Transaction_Report.png)|
| Delinquent Loan Report|![](Screenshots/raw_Delinquent_Loan_Report.png)|![](outputs/7_Delinquent_Loan_Report.png)|
| Loan Disbursement Report|![](Screenshots/raw_Loan_Disbursement_Report.png)|![](outputs/8_Loan_Disbursement_Report.png)|
| Loan Repayment Due Report|![](Screenshots/raw_Loan_Repayment_Due_Report.png)|![](outputs/9_Loan_Repayment_Due_Report.png)|
| Mortgage Portfolio Report|![](Screenshots/raw_Mortgage_Portfolio_Report.png)|![](outputs/10_Mortgage_Portfolio_Report.png)|

**5.2 Key Screenshots**

| Data Folder| Parameter Setup| Power Query Steps|
|----|----|----|
|![](outputs/1_data_folder.png)|![](outputs/2_Parameter_setup.png)|![](outputs/12_Power_Queries_Steps.png)|


| File Inventory - Before 05/07/2026| File Inventory - New Data 14/10/2026|
|----|----|
|![](outputs/3_output_Files_Inventory.png)|![](outputs/11_Files_Inventory_Post_Refreshing.png)|


### 📊 6. Outputs and Limitations

- **Outputs**<br>
Each report is loaded into a clean, structured Excel table. Analysts can use these tables for:<br>
  - PivotTables
  - Dashboards
  - Financial reporting
  - Power BI
  - Further data analysis in combination with data from other sources

- **Limitations**<br>
  - Target Rows Indicator: data rows in team are starting with specific characters, such as LN1, MTG, etc. If the indicator is used in different team or company, it needs to be adjusted.
  - Fixed-width column positions: columns in each report are fixed. When report formats change by the development team, position needs to be in line with the changes.
      
### 🛠️ 7. Tools Used & Skills Demonstrated

- **Tools**: Microsoft Excel, Power Query (M Language)
- **Skills**: Data Cleaning & Transformation, Folder-based Data Ingestion, ETL Design Principles
    
### 🚀 8. Key Achievement

This project replaces manual import routine with one parameter change and one Refresh All . 

👨‍💻 Author <br>
**Eddie Zhu** <br>
Data Analyst / Aspiring Actuary

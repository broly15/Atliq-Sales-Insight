# Project: Sales Insights of Data Analysis - AtliQ Hardware

![Sales Insights AtliQ](https://github.com/user-attachments/assets/d5dc81c9-6428-403d-936b-978600ffd1b7)


## Table of Contents

- [Problem Statement](#problem-statement)
- [Data Discovery](#data-discovery)
- [Data Analysis using MySQL](#data-analysis-using-mysql)
- [Data Cleaning and ETL (Extract, Transform, Load)](#data-cleaning-and-etl-extract-transform-load)
- [Data Modeling](#data-modeling)
- [Data Analysis (DAX)](#data-analysis-dax)
- [Build Dashboard or a Report](#build-dashboard-or-a-report)
- [Tools, Software and Libraries](#tools-software-and-libraries)

## Problem Statement

In this project, we performed a sales insights analysis for AtliQ Hardware, an India-based company that supplies computer hardware and peripherals to clients such as Surge Stores, Nomad Stores, etc. AtliQ Hardware’s head office is situated in Delhi, India, and it has multiple regional offices across the country.

The sales director faced significant challenges tracking sales in a dynamically growing market. Instead of relying on ad-hoc verbal reports from regional managers, a clear and data-driven picture of the business was needed. The goal was to create a simple data visualization tool that provides daily insights and empowers decision-making to boost sales.

## Data Discovery

#### Project Planning using AIMS Grid

AIMS Grid is a project management tool that consists of four components:
- **Purpose:** To unlock hidden sales insights and automate data gathering for improved decision support.
- **Stakeholders:** 
  - Sales Director
  - Marketing Team 
  - Customer Service Team
  - Data and Analytics Team
  - IT 
- **End Result:** An automated dashboard providing quick, up-to-date insights to support data-driven decision-making.
- **Success Criteria:**
  - A dashboard that uncovers sales order insights with the latest available data.
  - Empowering the sales team to make better decisions, resulting in a 10% cost saving.
  - Eliminating manual data gathering, saving 20% of business time to be reinvested in value-added activities.

#### Flowchart of Project Execution

![Flowchart](https://user-images.githubusercontent.com/118357991/231545034-7f6cc437-5683-44f1-92df-a671540ccae9.jpg)

## Data Analysis using MySQL

After data discovery, MySQL was used for analysis. The SQL database dump is provided in the `db_dump.sql` file. Download this file to your local machine and import it into MySQL Workbench.

**Steps:**

- **Importing Data to MySQL Workbench**

  ![Screenshot 1](https://user-images.githubusercontent.com/118357991/233262007-c36f58cd-df19-42b5-b9cb-4d72c0ef64a4.png)

  ![Screenshot 2](https://user-images.githubusercontent.com/118357991/233262064-b1fb8f0f-8c16-402d-adac-07784b81a2fe.png)

- **Analysis Overview:**

  - Identify and filter out garbage values (e.g., incorrect market entries and negative sales amounts).
  - Detect transactions in USD and convert them to INR where necessary.

**Sample Queries:**

1. Retrieve all customer records:  
   `SELECT * FROM sales.customers;`
2. Count total number of customers:  
   `SELECT count(*) FROM sales.customers;`
3. Retrieve transactions for Chennai (market code: Mark001):  
   `SELECT * FROM sales.transactions WHERE market_code='Mark001';`
4. Retrieve distinct product codes sold in Chennai:  
   `SELECT DISTINCT product_code FROM sales.transactions WHERE market_code='Mark001';`
5. Retrieve transactions for Mumbai (market code: Mark002):  
   `SELECT * FROM sales.transactions WHERE market_code='Mark002';`
6. Retrieve distinct product codes sold in Mumbai:  
   `SELECT DISTINCT product_code FROM sales.transactions WHERE market_code='Mark002';`
7. Retrieve transactions in USD:  
   `SELECT * FROM sales.transactions WHERE currency="USD";`
8. Join transactions with the date table for the year 2020:  
   `SELECT sales.transactions.*, sales.date.* FROM sales.transactions INNER JOIN sales.date ON sales.transactions.order_date = sales.date.date WHERE sales.date.year = 2020;`
9. Sum total revenue for 2020:  
   `SELECT SUM(sales.transactions.sales_amount) FROM sales.transactions INNER JOIN sales.date ON sales.transactions.order_date = sales.date.date WHERE sales.date.year = 2020 AND (sales.transactions.currency="INR" OR sales.transactions.currency="USD");`

*(Additional queries follow similar patterns.)*

## Data Cleaning and ETL (Extract, Transform, Load)

**Steps:**

1. **Connect MySQL Database with Power BI Desktop:**  
   Import all tables from the MySQL database into Power BI.

2. **Data Loading:**  
   Load the data into Power BI Desktop to build a star schema model.

3. **Data Transformation Using Power Query:**  
   - Filter rows with null or blank values.
   - Remove unwanted values (e.g., negative or zero amounts in transactions).
   - Convert USD to INR by adding a new column using a conditional formula:
     ```
     = Table.AddColumn(#"Filtered Rows", "norm_sales_amount", each if [currency] = "USD" then [sales_amount] * 75 else [sales_amount])
     ```

4. **Handling Duplicates:**  
   Identify and remove duplicate currency entries in MySQL Workbench to maintain data integrity.

## Data Modeling

After cleaning and transforming the dataset, a data model was created. The sales insights tables form a star schema as shown below:

![Data Model](https://user-images.githubusercontent.com/118357991/234016242-369bd02e-1ddf-4047-9be4-324c83bd8761.png)

## Data Analysis (DAX)

Key measures used in visualizations:

- **Profit Margin %:**  
  `DIVIDE([Total Profit Margin], [Revenue], 0)`
  
- **Profit Margin Contribution %:**  
  `DIVIDE([Total Profit Margin], CALCULATE([Total Profit Margin], ALL('sales products'), ALL('sales customers'), ALL('sales markets')))`
  
- **Revenue:**  
  `SUM('sales transactions'[sales_amount])`
  
- **Revenue Contribution %:**  
  `DIVIDE([Revenue], CALCULATE([Revenue], ALL('sales products'), ALL('sales customers'), ALL('sales markets')))`
  
- **Revenue LY:**  
  `CALCULATE([Revenue], SAMEPERIODLASTYEAR('sales date'[date]))`
  
- **Sales Quantity:**  
  `SUM('sales transactions'[sales_qty])`
  
- **Total Profit Margin:**  
  `SUM('sales transactions'[Profit_Margin])`

**Profit Target:**

- **Profit Target1:**  
  `GENERATESERIES(-0.05, 0.15, 0.01)`
  
- **Profit Target Value:**  
  `SELECTEDVALUE('Profit Target1'[Profit Target])`
  
- **Target Difference:**  
  `[Profit Margin %] - 'Profit Target1'[Profit Target Value]`

## Build Dashboard or a Report

Data visualizations were created using Microsoft Power BI Desktop, providing key insights into sales performance:

**Key Insights:**

| ![Sales Insights Visualization](https://user-images.githubusercontent.com/118357991/234025264-f5f1d7af-2ead-4d9a-b8ae-7524d200b7dd.jpg) |
| --- |

**Profit Analysis:**

| ![Profit Analysis 1](https://user-images.githubusercontent.com/118357991/234025629-3c2e3dcf-77fb-4c20-acdb-3f92604d1292.jpg) |
| --- |

| ![Profit Analysis 2](https://user-images.githubusercontent.com/118357991/234025913-3a09f076-e1c7-40a1-9983-d2c8767f252c.jpg) |
| --- |

## Tools, Software and Libraries

- MySQL
- Microsoft Power BI
- Power Query Editor
- DAX Language

---

 **This project provides valuable sales insights, helping businesses make data-driven decisions and optimize performance!**  
 **If you find this useful, don't forget to like the repository!**  


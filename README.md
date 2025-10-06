# Plan-Co.-Performance-Dashboard
**Plan Co. Performance Analysis**

**Project overview:**
In this project, a Power BI Dashboard is created using the given Excel file to analyze the gross profit performance of 2023 and 2024 against the previous years. The object of this project is to give a clear visualization of the company's financial health by comparing the performance between the year-to-date (YTD) and the prior-year-to-date (PYTD).
This helps the stakeholders understand how sales trends are changing, which regions are growing and where improvements can be made, along with plenty else.

**Key objectives answered:**
1. What is the Gross Profit performance for the current year-to-date?
2. How does the current YTD performance stacking up agaisnt last year?
3. Which countries are the top contributors to the decline or growth in gross profit?
4. What is the monthly trend of the gross profit and how does it do against last year's monthly performance?
5. How do indoor, landscape and outdoor products each factor into our monthly profits?
6. How do the individual customers perform in terms of gross profit value and gross profit margin (GP%)?

**Date Modeling**
<img width="1119" height="704" alt="image" src="https://github.com/user-attachments/assets/dc383b5c-ef89-41d5-8cc3-86162813ac58" />

Plant_Hierarchy[Product_Name_id] -> Fact_Sales[Product_id]
Accounts[Account_id] -> Fact_Sales[Account_id]
Date[Date] -> Fact_Sales[Date_Time]

**DAX Measures and Calculations**
A set of DAX measures are created and grouped inside the Measures Folder. Here are the key groups:
**1. Base Measures:**
- Sales = SUM(Fact_Sales[Sales_USD])
- COGS = SUM(Fact_Sales[COGS_USD])
- Quantity = SUM(Fact_Sales[quantity])
- Gross_Profits = [Sales] - [COGS]
- GP% = DIVIDE([Gross_Profits], [Sales])
**2. YTD and PYTD:**
- YTD_Gross_Profit = TOTALYTD([Gross_Profits], Fact_Sales[Date_Time])
- YTD_Quantity = TOTALYTD([Quantity], Fact_Sales[Date_Time])
- YTD_Sales = TOTALYTD([Sales], Fact_Sales[Date_Time])
- PYTD_Gross_Profit = 
  CALCULATE(
      [Gross_Profits],
      SAMEPERIODLASTYEAR('Date'[Date]),
      'Date'[Inpast] = TRUE <!-- This is a filter I made so that it only run until the same date of last year. So, if the date is Oct-25-2025, for the PYTD, it'll run            until Oct-25-2024. -->
  )
  

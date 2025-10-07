# Plan-Co.-Performance-Dashboard
**Plan Co. Performance Analysis**

**I. Project overview:**

In this project, a Power BI Dashboard is created using the given Excel file to analyze the gross profit, sales and quantity performance of 2023 and 2024 against the previous years. The object of this project is to give a clear visualization of the company's financial health by comparing the performance between the year-to-date (YTD) and the prior-year-to-date (PYTD).
This helps the stakeholders understand how sales trends are changing, which regions are growing and where improvements can be made, along with plenty else.

<img width="1311" height="737" alt="image" src="https://github.com/user-attachments/assets/98d139e5-ecf5-4518-bf84-03ee647e754c" />

**II. Key objectives answered:**

1. What is the Gross Profit performance, along with the Sales and Quantity for the current year-to-date?
2. How does the current YTD performance stacking up agaisnt last year?
3. Which countries are the top contributors to the decline or growth in gross profit, sales and quantity?
4. What is the monthly trend of the gross profit, sales and quantity and how does it do against last year's monthly performance?
5. How do indoor, landscape and outdoor products each factor into our monthly profits?
6. How do the individual customers perform in terms of gross profit, sales and quantity value versus gross profit margin (GP%)?

**III. Data Modeling**

<img width="1119" height="704" alt="image" src="https://github.com/user-attachments/assets/dc383b5c-ef89-41d5-8cc3-86162813ac58" />

1. Plant_Hierarchy[Product_Name_id] -> Fact_Sales[Product_id]
2. Accounts[Account_id] <-> Fact_Sales[Account_id]
3. Date[Date] -> Fact_Sales[Date_Time]

**IV. DAX Measures and Calculations**

A set of DAX measures are created and grouped inside the Measures Folder. Here are the key groups:

**1. Base Measures:**
- ```Sales = SUM(Fact_Sales[Sales_USD])```
- ```COGS = SUM(Fact_Sales[COGS_USD])```
- ```Quantity = SUM(Fact_Sales[quantity])```
- ```Gross_Profits = [Sales] - [COGS]```
- ```GP% = DIVIDE([Gross_Profits], [Sales])```

**2. YTD and PYTD:**
- ```YTD_Gross_Profit = TOTALYTD([Gross_Profits], Fact_Sales[Date_Time])```
- ```YTD_Quantity = TOTALYTD([Quantity], Fact_Sales[Date_Time])```
- ```YTD_Sales = TOTALYTD([Sales], Fact_Sales[Date_Time])```
- ```
  PYTD_Gross_Profit = CALCULATE(
      [Gross_Profits],
      SAMEPERIODLASTYEAR('Date'[Date]),
      'Date'[Inpast] = TRUE 
  )
  ```
  - The "'Date'[Inpast] = TRUE" is a filter I used so that it only run until the same date of last year. So, if the date is Oct-25-2025, for the PYTD, it'll run            until Oct-25-2024.

**3. Switching Logic:**

In order to change the dashboard from Gross Profit to Quantity or Sales, I made a switch to take the slicer's value as the parameter and select the according value based on that. If none are selected, it will default to Gross Profit.

``` 
Switch_YTD = 
VAR selected_value = SELECTEDVALUE(Slc_Values[Value])
VAR result =
    SWITCH(
        selected_value,
        "Sales", [YTD_Sales],
        "Quantity", [YTD_Quantity],
        "Gross Profit", [YTD_Gross_Profit],
        BLANK()
    )
RETURN result
```

A Switch_PYTD is also made in a similar way.

I also created a DAX to calculate the difference between YTD and PYTD based on the switches above:
```YTDvsPYTD = [Switch_YTD]-[Switch_PYTD]```

**4. Dynamic Title:**

To make the dashboard a bit better, I also make a DAX to change the title based on the selected metric:

```_Report Title = " Plant Co. " & SELECTEDVALUE(Slc_Values[Value]) & " Performance " & SELECTEDVALUE('Date'[Date].[Year])```

This just takes the company's name and concatinate with the selected slicer's metric and year from the Date table to create a dynamic title. 

**V. Insights and Findings:**

**Gross Profit:**

<img width="500" alt="image" src="https://github.com/user-attachments/assets/3776793b-f953-4364-a660-375012f46a6c" />
<img width="500" alt="image" src="https://github.com/user-attachments/assets/e19599b2-4479-45e7-8934-7f83d9d863f2" />


Based on these treemap and the waterfall chart, in 2023, China is the worst performing country with a -405K decline compared to the PYTD.

Moreover, several other countries also performed poorly, for example:
- The European Marker: Sweden (-63.43K), France (-50.35K), The Netherlands (-39.40K), Norway (-51.81K), Poland (-27.42K).
- The North America Market: The USA (-57.38K).

However, not all are in the negative. Contries such as Canada has a 56.4K and Portugal has a 103.8K increase compared to the PYTD.

These findings mean that the company should investigate the reason why China and countries in the EU market had such an enormous drop, and make an analysis on the success of Canada and Portugal's markets.

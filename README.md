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


Based on the treemap and the waterfall chart, in 2023, China is the worst performing country with a -405K decline compared to the PYTD.

Moreover, several other countries also performed poorly, for example:
- The European Marker: Sweden (-63.43K), France (-50.35K), the Netherlands (-39.40K), Norway (-51.81K), Poland (-27.42K).
- The North America Market: The USA (-57.38K).

However, not all are in the negative. Countries such as Canada (56.4K), the Phillipines (74.9K) and Portugal (103.8K) have a considerable increase compared to the PYTD.

These findings mean that the company should investigate the reason why China and countries in the EU market had such an enormous drop and make an analysis on the success of Canada, The Phillipines and Portugal's markets.

<img width="700" alt="image" src="https://github.com/user-attachments/assets/465e154d-3b5f-4d27-a008-87e887d03a1f" />

Based on the chart, we can see the this is a pretty inconsistant year for the company.

The second quarter was performing amazingly, with all 3 months making more than the PYTD. It peaked in April with a 531.9K in gross value. However, this met a consistent downturn until the end of the year, when the company made some recovery and achieved a 499.4K in gross value. Furthermore, the contributions of the 3 plant types are pretty stable throughout the year, with outdoor plants mostly staying on the top spot.

<img width="700" alt="image" src="https://github.com/user-attachments/assets/a877f9ac-ca24-41c1-9b72-e05663b452a0" />

- As we can see here, a large number of customers are in the bottom right quadrant of the chart (high value, low margin). So the company can review these customers for pricing and maybe do some upselling for margin improvement.
- Moreover, there are huge growth potentials for the groups in the top left quadrant (high margin, low value). So the sales team can try to come up with some strategy to grow these revenues.

**Quantity:**

<img width="679" height="97" alt="image" src="https://github.com/user-attachments/assets/f56c936f-251a-49d4-abd1-6f27ecb9bec7" />

The quantity is doing a little bit better than GP since it outweighted the PYTD by just over 17K in terms of units. However, this is a major issue, since the company is *selling more for less*. There are a few answers to this problem:
- Growth in less profitable products (or mayby regions) and decrease in more profitable products (or regions).
- Too many discounts were given.

<img width="700" alt="image" src="https://github.com/user-attachments/assets/f811fcd7-3463-44c7-8861-b808108c1d98" />

Right here, the Chinese and European markets are struggling to make sales. China had a major drop of 9.7K units, and the other European countries combined had more than 25K units. These are all traditionally good-performing countries

 <img width="700" alt="image" src="https://github.com/user-attachments/assets/a81d87d1-761c-4a25-adbb-c84fb29e965d" />

However, just like how the gross profit dashboard has displayed, countries like Portugal, the Philippines, and Canada all had exceptional months. This is why, despite losing so many units from China and the Europeans, the company's sales are still in the green.
Furthermore, the company still sees great performance from other European countries like Portugal (+11.5K), Germany (+4.2K) and Hungary (+3.1K), meaning that this is an issue with certain countries rather than the entire continent.

**Sales:**

<img width="500" alt="image" src="https://github.com/user-attachments/assets/6815b59b-8bb1-4789-ae46-7483d5e4e966" />
<img width="500" alt="image" src="https://github.com/user-attachments/assets/a28a8e13-76a2-431e-87af-79cbf6b9adaf" />

We can see that even though countries like Portugal or Canada contributed so much in 2023, the Total in the waterfall chart is still in the negative. From all of the charts, we can now know exactly that China is the source of this decline with a whopping $0.76 million drop.
Interestingly, even though Portugal had a wonderful year, *it's barely reaching $180K*, which means that most of the sales in this country are products that have a low profit margin, or we are simply just "buying revenue".

**Recommended actions:**

- An investigation is highly needed for the Chinese, and a few other European countries like Sweden, France to change the pricing strategy or supply chain issues, etc. Portugal also should be investigated to see why the number of units are is so high, yet the company can only profit $0.1M in this market.
- From the scatter chart, the company can ensure to have a steady supply for these accounts in the high-value, high-margin quadrant. Meanwhile, develop plans for customers in the other 3 quadrants, focusing on increasing the margin on the customers who have a high-volume first.

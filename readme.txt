
Power BI DAX Queries - GitHub Repository
Overview
This repository contains a collection of Power BI DAX (Data Analysis Expressions) queries designed for sales data analysis. The queries focus on calculating key business metrics such as sales changes, seasonal sales, and customer/product segmentation based on various factors like age and cost. Additionally, a dynamic calendar table is created for efficient time-based analysis and reporting.

Queries
1. Change in Sales
Calculates the percentage change in sales between the current month and the previous month.

DAX
Copy code
Change_in_sales = 
VAR previous_month =
    CALCULATE([Total_sales], PARALLELPERIOD(calendar_directquery_dimension[Date], -1, MONTH))
VAR output =
    DIVIDE([Total_sales] - previous_month, previous_month)
RETURN output
2. Festival Season Sales
Calculates the total sales for the months of October, November, and December (festival season).

DAX
Copy code
festival_season_sales = 
CALCULATE(
    [Total_sales],
    calendar_directquery_dimension[month num] = 10 ||
    calendar_directquery_dimension[month num] = 11 ||
    calendar_directquery_dimension[month num] = 12
)
3. Total Sales
Calculates the total sales by multiplying the quantity sold by the related product cost.

DAX
Copy code
Total_sales = 
SUMX(
    sales_fact_table_azureanalysis, 
    sales_fact_table_azureanalysis[Quantity_Sold] * RELATED(product_dimension_sql_live[Cost])
)
4. Calendar Dimension
Generates a calendar table based on the sales data with additional columns for year, quarter, month, and weekday.

DAX
Copy code
calendar_directquery_dimension = 
ADDCOLUMNS(
    CALENDAR(MIN('sales_fact_table_azureanalysis'[Date]), MAX('sales_fact_table_azureanalysis'[Date])),
    "Year", YEAR([Date]),
    "Quarter", "Q" & QUARTER([Date]),
    "Month", FORMAT([Date], "MMM"),
    "Month_num", MONTH([Date]),
    "Day", FORMAT([Date], "DDDD"),
    "Day_Number", WEEKDAY([Date])
)
5. Age Segment
Classifies customers into age segments such as "Young age", "Middle age", and "Old age".

DAX
Copy code
Age_segment = 
SWITCH(
    TRUE(),
    customer_dimension_sql[Age] > 50, "Old age",
    customer_dimension_sql[Age] >= 30, "Middle age",
    "Young age"
)
6. Cost Segment
Segments products into categories such as "Very costly", "High cost", "Average cost", and "Inexpensive" based on their cost.

DAX
Copy code
cost_segment = 
SWITCH(
    TRUE(),
    product_dimension_sql_live[Cost] > 150, "Very costly",
    product_dimension_sql_live[Cost] > 100, "High cost",
    product_dimension_sql_live[Cost] > 50, "Average cost",
    "Inexpensive"
)
Requirements
To use these DAX queries in Power BI, you will need:

Power BI Desktop: To create and visualize reports.
SQL Database or Azure Analysis Services: The data must be connected to Power BI through these services, especially for sales_fact_table_azureanalysis and product_dimension_sql_live.
Customer Data: Ensure the customer_dimension_sql table is available, as it includes customer age for segmentation.
Setup Instructions
Clone or download this repository.
Import the necessary tables (sales_fact_table_azureanalysis, product_dimension_sql_live, customer_dimension_sql) into Power BI.
Add the provided DAX queries as new measures and calculated columns in the Power BI "Modeling" section.
Use these measures to build insightful reports and dashboards in Power BI.
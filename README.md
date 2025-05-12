## Project Overview
This project was initiated to replace static sales reports with dynamic, interactive dashboards that provide clearer visibility into internet sales performance for 2025. The goal is to empower the sales team with real-time, filterable insights into what products are being sold, to whom, and how performance compares against the 2025 sales budget

## Objectives
- Extract and clean data from the AdventureWorks database using SQL.
- Build a Power BI dashboard to visualize key metrics and trends.
- Track actual sales against the 2025 budget.
- Allow filtering by product, customer, region, and sales period.
- Support data-driven decision-making across the sales team.

## Data Source
- AdventureWorks (SQL Server sample database)
- 2025 Budget Data (Excel sheet provided by Sales Manager

## Tools Used
- SQL Server Management Studio (SSMS): Used to write and execute SQL queries to extract and clean relevant data from the AdventureWorks database based on the project requirements.
- Microsoft Excel : Contained the 2025 sales budget used for comparison against actuals.
- Power BI :  Used for both data modeling (relationships, calculated columns, and measures) and visualization (dashboards, filters, and KPIs.
- DAX (Data Analysis Expressions) : Used within Power BI to calculate sales metrics, budget comparisons, and performance trends.
- GitHub – For project version control and sharing the dashboard documentation.

## Data Extraction and Cleaning 
### Extration and cleaning of DIM_customer table
```sql
-- Cleaned DIM_Customers Table --
SELECT 
  c.customerkey AS CustomerKey, 
  c.firstname AS First_Name, 
  c.lastname AS Last_Name, 
  CONCAT(c.firstname, ' ', c.lastname) AS Full_Name, 
  -- Combined First and Last Name,
  CASE c.gender WHEN 'M' THEN 'Male' WHEN 'F' THEN 'Female' END AS Gender, 
  --This CASE statement translates gender codes: 'M' becomes 'Male' 'F' becomes 'Female' Anything else becomes 'Other'
  c.datefirstpurchase AS Date_First_Purchase, 
  g.city AS Customer_City -- Joined in Customer City from Geography Table
FROM 
  [AdventureWorksDW2019].[dbo].[DimCustomer] as c 
  LEFT JOIN dbo.dimgeography AS g ON g.geographykey = c.geographykey 
ORDER BY 
  CustomerKey ASC -- Sort list by CustomerKey;
```
### Extraction and Cleaning of DIM_product Table
```sql
-- Cleaned DIM_Products Table --
SELECT 
  p.[ProductKey], 
  p.[ProductAlternateKey] AS Product_ItemCode, 
  p.[EnglishProductName] AS Product_Name, 
  ps.EnglishProductSubcategoryName AS Sub_Category, 
  -- Joined in from Sub Category Table
  pc.EnglishProductCategoryName AS Product_Category, 
  -- Joined in from Category Table
  p.[Color] AS Product_Color, 
  p.[Size] AS Product_Size, 
  p.[ProductLine] AS Product_Line, 
  p.[ModelName] AS Product_ModelName, 
  p.[EnglishDescription] AS Product_Description, 
  ISNULL (p.Status, 'Outdated') AS Product_Status ---- Replace NULL status with 'Outdated'
FROM 
  [AdventureWorksDW2019].[dbo].[DimProduct] as p 
  LEFT JOIN dbo.DimProductSubcategory AS ps ON ps.ProductSubcategoryKey = p.ProductSubcategoryKey 
  LEFT JOIN dbo.DimProductCategory AS pc ON ps.ProductCategoryKey = pc.ProductCategoryKey 
order by 
  p.ProductKey asc; -- Sort results by ProductKey in ascending order
```
### Extraction and Cleaning of DIM_calender Table
```sql
-- Cleaned DIM_Date Table --
SELECT 
  [DateKey], 
  [FullDateAlternateKey] AS Date, 
  [EnglishDayNameOfWeek] AS Day, 
  [EnglishMonthName] AS Month, 
  Left([EnglishMonthName], 3) AS MonthShort, 
  --  -- Get 3-letter abbreviation of the month
  [MonthNumberOfYear] AS MonthNo, 
  [CalendarQuarter] AS Quarter, 
  [CalendarYear] AS Year 
FROM 
  [AdventureWorksDW2019].[dbo].[DimDate] 
WHERE 
  CalendarYear >= 2023;  -- Filter DimDate 
```
### Extraction and Cleaning of FACT_internetsales table
```sql
-- Cleaned FACT_InternetSales Table --
SELECT 
  [ProductKey], 
  [OrderDateKey],  
  [DueDateKey], 
  [ShipDateKey], 
  [CustomerKey], 
  [SalesOrderNumber], 
  [SalesAmount]  
FROM 
  [AdventureWorksDW2019].[dbo].[FactInternetSales]
WHERE 
  LEFT (OrderDateKey, 4) >= YEAR(GETDATE())-2  -- Ensures we always only bring two years of date from extraction.
ORDER BY
  OrderDateKey ASC; -- Sort list by orderdate ascending order
```
### Insights
#### Top 5 Customers Drive Most of the Revenue
- Over 50% of internet sales come from a small customer group.
- Highlights a strong dependence on a small group of customers.

#### Bikes Category Exceeds 2025 Budget
- Bikes consistently outperform other categories.
- Surpassed monthly budget targets by a significant margin.

#### Accessories and Clothing Underperform
- Fell short of 2025 budget by over 25%.
- Shows weak traction across most customer segments

#### Sales Dip in Q2 Across Categories
- Q2 underperformed despite steady demand before and after.
- Affects year-to-date budget alignment

#### London and Paris Dominate Sales Volume
- These cities account for the highest revenue and customer count.
- Clear regional sales hotspots

## Recommendations
#### Strengthen Top Customer Relationships
- Personalize engagement strategies for key accounts.
- Prioritize retention, upselling, and exclusive offers

#### Invest in High-Performing Products (Bikes)
- Increase inventory, promotions, and marketing for Bikes.
- Track performance by region and rep for deeper insights.

##### Improve Strategy for Underperforming Categories
- Repackage Accessories and Clothing into bundles.
- Reassess pricing and visibility in marketing efforts

#### Investigate Q2 Performance Drop
- Review supply chain, seasonality, and sales activities.
- Adjust future Q2 strategies accordingly

#### Expand Presence in High-Growth Cities
- Launch city-specific campaigns for London and Paris.
- Consider increasing sales force or partnerships there.

## Challenges Faced
- Date Misalignment: The budget and sales data covered different periods, causing inconsistencies in comparisons.
- Incomplete 2025 Sales Data: Sales records existed only for January 2025, while the budget spanned up to June.
- Customer Data Cleaning: Required SQL transformations like merging names, decoding gender, and linking city data


[View full power bi report here](https://app.powerbi.com/groups/me/reports/6dee778d-7465-4bf0-b9bb-aba970e913c8?ctid=6bd0734e-4fc4-4b1c-be23-6e5b6ed0d481&pbi_source=linkShare&bookmarkGuid=066935f8-0f55-4bc4-89da-f6e16cb5ade4)





















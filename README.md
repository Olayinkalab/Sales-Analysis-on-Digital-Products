# Data Analysis Project - Sales Management 

### Business Request 

The business request for this anaysis was an executive sales report for sales managers.Based on the request that was made from the business,we following create a user stories to fulfill the bsuiness requirement into technical solutiions that ensure the acceptance criteria were maintained throughout the project to meet stakeholder expectations. 

### Business Demand Overview:
-	Requester: Chris – Sales Manager
-	Value of Change: Visual dashboards and improved Sales reporting or follow up or sales force
-	Necessary Systems: Power BI, CRM System
-	Other Relevant Info: Budgets have been delivered in Excel for 2021

### User Stories:
|No |As a role|I want to|So that I |Acceptance Criteria|
|--------|--------|--------|--------|--------|
|1|Sales Manager|To get a dashboard overview of internet sales|Can get insight on which customers and products sells the best|A Power BI dashboard which updates data daily|
|2|Sales Representative|A detailed overview of Internet Sales per Customers|Can follow up my Top customers who we can sell more to|A Power BI dashboard which allows me to filter data for each customer|
|3|Sales Representative|A detailed overview of Internet Sales per Products|Can follow up my Products that sells the most|A Power BI dashboard which allows me to filter data for each Product|
|4|Sales Manager|A dashboard overview of internet sales|Follow sales over time against budget|A Power Bi dashboard with graphs and KPIs |


### Data Sources
Sample Structured data from AdventureWorksDW2019: "Dim_Calender.csv","Dim_Customer.csv","Dim_Internet_sales.csv","Dim_Products.csv","SalesBudget.csv" each derived using sql statement to seleted required column for analysis.

### Tools

- Microsoft SQL Server Management Studio for Analysis [Download here](https://www.microsoft.com/en-ca/sql-server/sql-server-downloads)
- Excel 
- PowerBI for visualization [Download Here](https://www.microsoft.com/en-us/download/details.aspx?id=58494)

### Data Cleaning and Transformation (SQL)
1. Identify Fact Table vs Dimensional Tables.
2. Identify necessary Tables for analysis based on business request and user stories overview using SQL.
3. write SQL Statement to cleanse data.
4. import CSV files into PowerBI to create data model.
5. Upload Budget file to build relationships .

### Exploratory Data Analysis 
- who are our Top customers?
- whcih Product is are Top sellers?
- Top selling geographical locations?
- what are the peak sales periods? 
- which sales representatives are performing? 
- What are the overall sales trends against our Budgets?

### Data Analysis
To create the required data model for analysis based on business need and the defined user stories the following tables were extracted using the below sql statement 
One data sourse was shared by the sales manager via email, this was connected in the data model to complete the analysis. 

Below is the sql statement to cleans and transform the necessary data before analysis 

### Dim_Calendar 
``` 
--/clean DimDate table to get the column needed---
SELECT 
  [DateKey], 
  [FullDateAlternateKey] AS Date, 
  --[DayNumberOfWeek],
  [EnglishDayNameOfWeek] AS Day, 
  --[SpanishDayNameOfWeek],
  --[FrenchDayNameOfWeek],
  --[DayNumberOfMonth],
  --[DayNumberOfYear],
  --[WeekNumberOfYear] AS WeekNr,
  [EnglishMonthName]AS Month, 
  Left([EnglishMonthName], 3) AS MONTHSHORT, 
  --Use for frontend date navigation and front end graph
  --[SpanishMonthName],
  --[FrenchMonthName],
  [MonthNumberOfYear] AS MonthNo, 
  [CalendarQuarter] AS Quarter, 
  [CalendarYear] AS Year --[CalendarSemester],
  --[FiscalQuarter],
  --[FiscalYear],
  -- [FiscalSemester]
FROM 
  [dbo].[DimDate] 
WHERE 
  CalendarYear >= 2019
```
### Dim_Customer
```
-- Cleansed DIM_Customers Table --
SELECT 
  C.CustomerKey AS CustomerKey, 
  --[GeographyKey],
  --[CustomerAlternateKey],
  --[Title],
  c.firstname AS [First Name], 
  -- [MiddleName]
  c.lastName AS [Last Name], 
  c.firstname + ' ' + lastname AS [Full Name], 
  --to concatename first and last name for easy analysis
  --[NameStyle],
  --[BirthDate],
  --[MaritalStatus],
  --[Suffix],
  CASE C.gender WHEN 'M' THEN 'MALE' WHEN 'F' THEN 'FEMALE' END AS Gender, 
  --[EmailAddress],
  --[YearlyIncome],
  --[TotalChildren],
  --[NumberChildrenAtHome],
  --[EnglishEducation],
  --[SpanishEducation],
  --[FrenchEducation],
  --[EnglishOccupation],
  --[SpanishOccupation],
  --[FrenchOccupation],
  --[HouseOwnerFlag],
  --[NumberCarsOwned],
  --[AddressLine1],
  --[AddressLine2],
  --[Phone],
  C.DateFirstPurchase AS DateFirstPurchase, 
  --[CommuteDistance],
  g.city AS[Customer City] --joined city from customer geography table
FROM 
  [AdventureWorksDW2019].[dbo].[DimCustomer] as c 
  LEFT JOIN dbo.DimGeography AS g ON G.GeographyKey = C.GeographyKey 
ORDER BY 
  CustomerKey ASC --Ordering the List by CustomerKey
```
### Dim_Products

```
--cleansed DIM_Products table--
SELECT p.[ProductKey],
      p.[ProductAlternateKey] AS ProductitemCode,
      --[WeightUnitMeasureCode],
      --[SizeUnitMeasureCode],
     p.[EnglishProductName] AS [Product Name],
	 ps.EnglishProductSubcategoryName AS [Sub Category], --joined from sub Category table
	 pc.EnglishProductCategoryName AS [Product Category], --joined from Category table
      --[SpanishProductName],
     --[FrenchProductName],
      --[StandardCost],
      --[FinishedGoodsFlag],
      p.[Color] AS [Product Color],
      --[SafetyStockLevel],
      --[ReorderPoint],
      --[ListPrice],
      p.[size] AS [Product Size],
      --[SizeRange],
      --[Weight],
     --[DaysToManufacture],
      p.[ProductLine] as [Product Line],
      --[DealerPrice],
      --[Class],
      --[Style],
      P.[ModelName] as[Product Mode],
      --[LargePhoto]
      p.[EnglishDescription] AS [Product Description],
      --[FrenchDescription]
      --[ChineseDescription]
      --[ArabicDescription]
      --[HebrewDescription]
      --[ThaiDescription]
      --[GermanDescription]
      --[JapaneseDescription]
      --[TurkishDescription]
      --[StartDate]
      --[EndDate]
     ISNULL(P.Status,'Outdated') AS [Product Status]
  FROM [dbo].[DimProduct] as P
  LEFT JOIN dbo.DimProductSubcategory AS ps ON ps.ProductSubcategorykey = p.Productsubcategorykey
  LEFT JOIN dbo.Dimproductcategory AS pc ON ps.productCategoryKey = pc.ProductCategoryKey
  ORDER BY
  P.ProductKey ASC
```
### Internet_sales 

```
--/Cleansed FACT_InternetSales Table---
SELECT [ProductKey],
      [OrderDateKey],
      [DueDateKey],
      [ShipDateKey],
      [CustomerKey],
      --[PromotionKey],
      --[CurrencyKey],
      --[SalesTerritoryKey],
      [SalesOrderNumber],
      --[SalesOrderLineNumber],
      --[RevisionNumber],
      --[OrderQuantity],
      --[UnitPrice]
      --[ExtendedAmount],
      --[UnitPriceDiscountPct],
      --[DiscountAmount],
      --[ProductStandardCost],
      --[TotalProductCost],
      [SalesAmount]
      --[TaxAmt],
      --[Freight],
      --[CarrierTrackingNumber],
      --[CustomerPONumber],
      --[OrderDate],
      --[DueDate],
      --[ShipDate],
	  FROM 
  [dbo].[FactInternetSales]
  WHERE 
  LEFT (Orderdatekey,4)>= YEAR(GETDATE()) -2 -- to only bring the previous 2 years of date we need for analysis
  ORDER BY
  OrderDateKey ASC
```

### Creating Data Model
The Data Model shows how the FACT_Budget has been connected to the Fact_Internet_sales and other neccessary DIM_tables
  

<img width="606" alt="Data Model" src="https://github.com/user-attachments/assets/8449920e-db58-421f-b2e7-e38c87e54ac5">


### Sales Dashboard 
This is the sales management dashboard with one page as the sales overview other pages which focused on combining tables for necessary analysis and visualisation to show sales per customers and products over the specific period of time.

<img width="858" alt="sales Overview" src="https://github.com/user-attachments/assets/212bb876-d922-48be-b40f-f2e0abe39b40">



 <img width="854" alt="Customer Insights" src="https://github.com/user-attachments/assets/cf68f565-19a4-471e-bcaa-46073679c595">

###Findings
The internet sales have been steadily incresing over the past year , with a noticeable peak during the holidays seasons
95% of this sales are from Bikes purchase which invarably is the best performing products in terms of sales and revenue.
Customer segment with high lifetime value (LTV) Should be tergeted for marketing effort
Most 

  







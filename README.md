# Sales Analysis using SQL

## Company activity/ field : World wide importers (WOWI) Ltd - Sales Analysis

## Table of Content
-  [Project Overview](#project-overview)
-  [Data Sources](#data-sources)
-  [Tools](#tools)
-  [Data cleaning ](#data-cleaning )
-  [Exploratory data analysis](#exploratory-data-analysis)
-  [Data analysis](#data-analysis)
-  [Findings](#findings)
  
### Project overview
---

---- **Company information**

Is  a novelty goods importer and wholesale distributor, operating from the San Francisco bay area
  
---- **Delivrables**

• Help the marketing team make decisions for a new Halloween social media campaign.
• Help the delivery team make decisions for a delivery efficiency trial.
• Help onboard a new member of the finance team by analyzing data across time.
• Help the product team by evaluating the performance of specific products, including a new chilled products line
 


 ### Data sources
 ---

*Company  Data  ,the dataset use for this analysis is  WIWO DW  . we have data on sales , promotions , products , stores and managers*

### Tools
---

  - Excel-power query : Data cleaning
  - Excel-power pivot : Data model 
  - Excel-power pivot-DAX : Calculating mesures
  - Excel-pivot table : Dynamic tables
  - Excel-pivot chart : Dynamic visualisation
      - [Download Excel here](https://microsoft.com)

### Data cleaning 
---

    1.No duplicate found
	2.No Null/NA value found
	3.ALL columns had correct value format
	  
We just had to transform the data on sales , promotions , products , stores and managers into tables

### Exploratory data analysis
---

EDA involved exploring  the sales data to  answer key question , such as :

  - What is the overrall  sales trends ?
  - what is the Sales units, growth sales and margin per store  ?
  - what is the Sales units, growth sales and margin per category  ?
  - what is the Sales units, growth sales and margin per brand  ?

### Data analysis
---

```DAX
To create the model , the source data has been normalized  in order to Reduced storage space , Easier maintenance and Improved query speed . The normalization gave
	  - One fact table ; Sales Fact Table
	  - Five dimension tables  ; Product Dimension Table , Store Dimension Table , Date Dimension Table , Manager Dimension Table and Commission Dimension
        Table
	  - Created relationship between Sales Fact Table and dimension tables to build the model
	  - Creating implicit measures

--Measure to calculate units sold :: Units:=sum(Sales[Units Sold])
--Measure to calculate Margin Amount :: MarginAmt:=sum([MarginDollars])
--Measure to calculate sales :: Sales:=SUMX(Sales,[Units Sold]*[UnitPrice])
--Measure to calculate margin percentage :: MarginPct:=[MarginAmt]/[Sales]
--Measure to calculate sales days  ::  SalesDays:=DISTINCTCOUNT([DateID])
--Measure to calculate sales per days :: SalesPerDay:=[Sales]/[SalesDays]
--Calculating Sales percentage of total of product :: Pdt_Sales_PCT:=[Sales]/CALCULATE([Sales],ALL(Dim_Products))
--Calculating Sales percentage of total of managers  :: Mger_sales_PCT:= [Sales] / CALCULATE([Sales],ALL(Dim_Managers))
--Calculating Sales percentage of total of days  :: Day_Sales_PCT:=
   var TotalSales = CALCULATE([Sales],ALLSELECTED(Dim_Dates))
   var Daysales =[Sales]
   return  TotalSales /  Daysales
--calculating promotion sales  :: PromoSales:=CALCULATE([Sales],Sales[Promo]<>"")
--Calculating promotion sales percentage :: Promo_sales_PCT:=[PromoSales] /[Sales]
--Calculating sales of previous year :: SalesPY:=CALCULATE([Sales],SAMEPERIODLASTYEAR(Dim_Dates[Date]))
--Calculating sales growth :: SalesGrowth:=IFERROR([Sales]/[SalesPY]-1, "NA")
--Calculating margin growth ::MarginGrowth:=
    var MarginPY = CALCULATE([MarginPct],SAMEPERIODLASTYEAR(Dim_Dates[Date])) 
    return [MarginPct] - MarginPY		
--Calculating sales per day YTD :: SalesPerdayYTD:=CALCULATE([SalesPerDay],DATESYTD(Dim_Dates[Date]))



 ##Function use
	 
		-- Creating a calculated column for the store size using the switch function :: =SWITCH(Dim_Stores[Store Type],"SM","Small","MED","Medium","WAREHOUSE","WAREHOUSE","OTHER")
			
	  - Other calculation
			
		-- Creating a calculated column for the MarginDollar  :: =[Units Sold]*[UnitPrice]*[RawMargin]
		-- Creating a calculated commission column in the sales table :: =RELATED(Dim_Commission[Commission])*[Units Sold]*[UnitPrice]	
		
## Renaming of  Measures name in pivot table
		
		   YoY sales(Salegrowth) , means year over year sales
		   pdt_sales_PCT share(share) in the business overview
		   Margin(MarginPct)
		   MarginGrowth(Yoy Margin)
		   promo_sales_PCT (Promo%)
		   Day_Sales_PCT(share)in the store performance
		   SalesPerDay(Sales/Day)
		   SalesPerdayYTD (Sales/day YTD)
		   SalesDays(days worked)

		
```

### Findings
---

-- On the business over view we are able to dive in the performance by store , category and brand of bed that the company sells.
 
-- On the store  performance overview , we are able to see which day of the week generated the most sale , performance of each category on the business . 
   Also we can assess the performance of each manager responsible for the store at any given period
 

💻💻  


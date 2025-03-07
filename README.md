# Sales Analysis using SQL

## Company activity/ field : World wide importers (WOWI) Ltd - Sales Analysis

## Table of Content
-  [Project Overview](#project-overview)
-  [Data Sources](#data-sources)
-  [Tools](#tools)
-  [Data cleaning ](#data-cleaning )
-  [Exploratory data analysis](#exploratory-data-analysis)
-  [Data analysis](#data-analysis)
  
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

*Company  Data  ,the dataset use for this analysis is  WIWO DW . As alternative wow folder that conatin the fact and the dimenion tables in excel can be use*

### Tools
---

  - Azure data studio and SQL : querying data

## Exploratory data analysis
---

--How many rows are in the FactSale table? 

``` SQL
		select COUNT(*) as Number_of_Rows
		from factSale ;

	or

		select COUNT(*) as [Number of Rows]
		from factSale ;
		
```
--What data type is the Quantity column in FactSale? 

``` SQL
By checking the Database and the documentation we see that it is an integer
	
```
--How many columns in the FactOrder table can contain null values? 

``` SQL
By checking the Database , running the below queries, we see that 3 columns has null values : Picked Date Key , Picker Key and WWI Backorder ID

			select *
			from factOrder
			order by [WWI Backorder ID] ASC ;
                  and
			select *
			from factOrder
			order by [Picked Date Key] ASC ;
                  and
			select *
			from factOrder
			order by [Picker Key] ASC ;	
```

--Does the Staging_factSale table contain any data?

``` SQL
By running the below queries, we see that Staging_factSale , have no data  . Staging table are use in the ETL process , and is been cleared when the data has been loaded

			SELECT* FROM
			Staging_factSale; 
```

--Do the collation properties of the server suggest the database is case sensitive? 


``` SQL
By running the below queries, it gives as result: SQL_Latin1_General_CP1_CI_AS . CI stand for case insensitive , so the database is not case sensitive

                SELECT CONVERT(varchar(256),ServerProperty('collation'))

```
--What does the lineage column mean in the fact and dimension tables? 

``` SQL
According to the documentation provided by the Data enginer ,The lineage table combined with the lineage columns helps to understand which data was updated and when.
	
```

--What key is enforced between FactSale and dimCustomer dimension?

``` SQL
we can see in the database in the factsale table in the keys , that the enforced key is : FK_Fact_Sale_Customer_Key_Dimension_Customer
	
```

----Exploration of customer data-----
-	How would you best describe the type of customers that we have? 
     
``` SQL
Base on the query below , we that there is mainly two buying group Tailspin Toys  and Wingtip Toys . They all buy same category of good Novelty 

```

--How many rows does the dimCustomer table have? 


``` SQL
By running the below queries, we can see that it has 403

		select count(*) NumberOfRowDimCustumer
		from dimCustomer;	
```

--Does the customer dimension include an entry for Unknown? 

``` SQL
By running the below queries, we can see that , the custumer dimension has one entry for unknown

		select customer
		from dimCustomer 
		where customer ='unknown';	
```

--How many known customers do we have?

``` SQL
By running the below queries, we can see that we have only customer 402

		select count(*) as NumberOfCustomer
		from dimCustomer
		where customer != 'unknown';	
```

 ----Exploration of product data-----
 -	How many foreign keys does the DimStockItem table have? 

``` SQL
we can see in the database and the  based on ERD that it has zero foreign key and one primary key

```
--How many rows are in the dimStockItem table? 

``` SQL
By running the below queries, we can see that there is 229 rows

		select *
		FROM DimStockItem ;

	OR
		select COUNT(*) as PRODUCT
		FROM DimStockItem ;	
```

--How many known products are we dealing with? 

``` SQL

By running the below queries, we can see that we have only products 228

		select COUNT(*) 
		FROM DimStockItem 
		where [Stock Item] <> 'unknown'
```
--What does the “Is Chiller Stock” column mean?
``` SQL
According to the documentation provided by the Data enginer , to check if the stock item need to be constantly refrigerated or not

```
--How many unique colors of products are there in the inventory


``` SQL
By running the below queries, we can see that we have 8 unique color of product in the inventory

		select distinct Color as productColors
		FROM DimStockItem ;

	or

		select count (distinct Color) as productColors
		FROM DimStockItem ;
```

### Data analysis
---

The marketing team are running a new Halloween campaign to reward people who share content on social media. We plan to ship a low cost item to them for free.

-	What is the unit price of the cheapest product that we currently sell (excluding Unknown Items)?  By running the below queries, the unit price of the cheapest product is 0,66

``` SQL
select 
 		MIN([Unit Price]) as UnitPrice
		FROM DimStockItem 
		where  [Stock Item] != 'Unknown'
	
```

--What is the name of the cheapest product that we currently sell ?  By running the below queries, we see that the name of the cheapest product is  : 3 kg Courier post bag (White) 300x190x95mm

``` SQL

select
		[Stock Item] as productName,
		[Unit Price]
		From DimStockItem
		WHERE [Unit Price] = 
    		(select 
 		MIN([Unit Price]) as UnitPrice
		FROM DimStockItem 
		where  [Stock Item] != 'Unknown'
		)
```
Notice that many of the cheapest items relate to packaging materials. Exclude all items that contain the word box, bag, or carton in their names.


What is the cheapest non-packaging related product? By running the below queries, we see that the cheapest non-packaging related product is : Packing knife with metal insert blade (Yellow) 9mm 

``` SQL
select 
		[Stock Item] AS ProductName,
		[Unit Price] as UnitPrice
		FROM DimStockItem 
		where [Stock Item] <> 'Unknown'  and 
		[Stock Item]  not LIKE '%carton%'  and
		[Stock Item] not LIKE '%box%'  and
		[Stock Item] not LIKE '%bag%'  
		order by  UnitPrice ASC ;
```

The marketing team have got back to you:
- Since the winner of the social media campaign might be a child, they don’t want to send a knife.
- Instead they have suggested that we send a mug or shirt to the chosen winner.
- The product should also be black if possible.


--Find a list of products that contain mug or shirt in their name. How many are there? By running the below queries, we have the list of product that contain mug or shirt

```SQL

select 
		[Stock Item] AS ProductName

		FROM DimStockItem 
		where 
		[Stock Item]  LIKE '%mug%'  OR
		[Stock Item]  LIKE 'mug%'  OR
		[Stock Item]  LIKE '%mug' OR
		[Stock Item]  LIKE '%shirt%' OR
		[Stock Item]  LIKE 'shirt%' OR 
		[Stock Item]  LIKE '%shirt' 

```
By running the below queries, we have 68 products that contain mug or shirt in their name

```SQL
select COUNT([Stock Item])

		FROM DimStockItem 
		where 
		[Stock Item]  LIKE '%mug%'  OR
		[Stock Item]  LIKE 'mug%'  OR
		[Stock Item]  LIKE '%mug' OR
		[Stock Item]  LIKE '%shirt%' OR
		[Stock Item]  LIKE 'shirt%' OR 
		[Stock Item]  LIKE '%shirt

```

--Since it’s Halloween, how many products meet this description, and are also black ? By running the below queries, we have the lists of products that contain mug or shirt in their name and is black

```SQL

select 
		[Stock Item]

		FROM DimStockItem 
		where 
		([Stock Item]  LIKE '%mug%'  OR
		[Stock Item]  LIKE 'mug%'  OR
		[Stock Item]  LIKE '%mug' OR
		[Stock Item]  LIKE '%shirt%' OR
		[Stock Item]  LIKE 'shirt%' OR 
		[Stock Item]  LIKE '%shirt' ) AND

		[Stock Item]  LIKE '%black%'


```
By running the below queries, we have the 34  products that contain mug or shirt in their name and is black

```SQL

select 
		COUNT([Stock Item])

		FROM DimStockItem 
		where 
		([Stock Item]  LIKE '%mug%'  OR
		[Stock Item]  LIKE 'mug%'  OR
		[Stock Item]  LIKE '%mug' OR
		[Stock Item]  LIKE '%shirt%' OR
		[Stock Item]  LIKE 'shirt%' OR 
		[Stock Item]  LIKE '%shirt' ) AND

		[Stock Item]  LIKE '%black%'

```

--What is the WWI Stock Item ID of the cheapest product meeting the above conditions? If multiple products have the same price, choose the one with the lowest WWI Stock Item ID. 
By running the below queries, we  see that,  DBA joke mug - mind if I join you? (Black) with 17 as WWI Stock Item ID that has the cheapest price.

```SQL

select 
		[WWI Stock Item ID],
		[Stock Item] as ProductName,
		[Unit Price]  as UnitPrice

		FROM DimStockItem 
		where 
		([Stock Item]  LIKE '%mug%'  OR
		[Stock Item]  LIKE 'mug%'  OR
		[Stock Item]  LIKE '%mug' OR
		[Stock Item]  LIKE '%shirt%' OR
		[Stock Item]  LIKE 'shirt%' OR 
		[Stock Item]  LIKE '%shirt' ) AND

		[Stock Item]  LIKE '%black%'

		order by UnitPrice ASC;


```
--What is the markup of WWI Stock Item ID 29? markup = ( retail price – unit price ) / unit price
By running the below queries, we  see that, markup of WWI Stock Item ID 29 is 0.4953846153846153846

```SQL

SELECT
		[WWI Stock Item ID],
		[Stock Item] as ProductName,
		[Unit Price]  as UnitPrice,
		[Recommended Retail Price],
		([Recommended Retail Price]-[Unit Price]) / ([Unit Price]) as Markup

		From dimStockItem
		where [WWI Stock Item ID] =29


```
--putting the answer to 34 decimal

```SQL

SELECT
		[WWI Stock Item ID],
		[Stock Item] as ProductName,
		[Unit Price]  as UnitPrice,
		[Recommended Retail Price],
		cast(([Recommended Retail Price]-[Unit Price]) / ([Unit Price])as decimal(8,4)) as Markup

		From dimStockItem
		where [WWI Stock Item ID] =29



```
Delivery optimization

The delivery team is exploring ideas to improve delivery efficiency. Instead of making deliveries to individual customer stores, the team wants to group deliveries by postcode
and buying group. Buying groups purchase inventory on behalf of the stores within the group.

--How many customers are in each buying group? 

```SQL

	--By running the below queries, we  see that, there are two buying groups and each have 201 customers

		SELECT 
		distinct [Buying Group],
		count(customer) as NumberOfCustomers

		from dimCustomer 
		WHERE [Buying Group] <> 'N/A'
		GROUP by [Buying Group];

```


We are trialling a new delivery process with Wingtip Toys. We need to identify clusters of shops from this buying group that are near each other.
-	Do any postcodes have more than 3 Wingtip Toys shops? If so, which postcode? 
-	By running the below query, we see that, the poste code 90683 have more than 3 Wingtip Toys shops

```SQL
select  
 		[Postal Code],
 		[Buying Group],
 		count([Postal Code]) as PostalCodeOccurrence
 		FROM dimCustomer
		WHERE [Buying Group] = 'Wingtip Toys' 
		GROUP by  [Postal Code] , [Buying Group] 
		Having count([Postal Code]) >3 

	OR

		select  
 		[Postal Code]
 		FROM dimCustomer
		WHERE [Buying Group] = 'Wingtip Toys' 
		GROUP by  [Postal Code] 
		Having count([Postal Code]) >3


```
--If a postcode has been identified, which of the following stores should be included in the delivery efficiency trial? By running the below query, we see that, we will have only 4 shops included in the trial

```SQL

select  
	 	[Postal Code],
 		Customer
 		FROM dimCustomer
		WHERE [Buying Group] = 'Wingtip Toys'  AND
		[Postal Code] IN  ( select  
 		[Postal Code]
 		FROM dimCustomer
		WHERE [Buying Group] = 'Wingtip Toys' 
		GROUP by  [Postal Code] 
		Having count([Postal Code]) >3 )

```

The research and development / strategy department questions


Employee Dimension. What proportion of our workforce works in sales?  Based on below query we see that the percentage of sales employee is 0.5263



```SQL


SELECT 

Cast( Count([Is Salesperson]) /   (SELECT
 CAST( count([Is Salesperson])as decimal(8,2))
  from dimEmployee
  WHERE Employee !='Unknown') as decimal(8,4)) As percentageOfSalesEmployee

 from dimEmployee

  WHERE Employee !='Unknown' and [Is Salesperson] = 1 

```

-	City Dimension. Which sales territory has the highest population? From the below  query, is Southeast that has the highest population
-	City Dimension. How many cities are in the above territory? From the below  query, we can see the number of cities in the  Southeast territory is 9063
-	City Dimension. What is the approx. population of the biggest city in that territory? From the below  query, in the Southeast territory, the biggest city has a population of 821784
-	City Dimension. What is the total population across all sales territories?  From the below  query , the total population of all sales territories is 227338926


```SQL

SELECT

 isnull([Sales Territory],'Total') as SalesTerritory,
 sum([Latest Recorded Population]) as TotalPopulation,
 count([WWI City ID]) as NumberOfCity,
 MAX([Latest Recorded Population]) as PopulationInBiggestCity
 

 From dimCity 
 where City <> 'Unknown'

 GROUP by rollup ([Sales Territory])

order by TotalPopulation DESC



```
FINANCE, SALES(sales over time) AND PROFIT RELATED CONCER

-	what granularity is the factSales table : Products sold as part of an invoice
-	From the ERD, what type of relationship exist between the factSale and dimdate tables : one to many relationship
-	What is the maximum fiscal year in the dimDate table : Base on the below query , the maximum fiscal year is 2017

```SQL

SELECT  
max([Fiscal Year])
      
  FROM [dbo].[dimDate]

```
--How many fiscal year do we have sales data for :  Base on the below query ,  we have sales only in 4 fiscal year

```SQL
SELECT
D.[Fiscal Year],
SUM(S.[Total Excluding Tax]) AsTotalSalesExcludingTaxes
from factSale S JOIN 
dimDate D ON S.[Invoice Date Key] = D.[Date]

GROUP BY D.[Fiscal Year]
ORDER BY  D.[Fiscal Year] ASC
```

sales by fiscal year

-	Calculate a report of [Sales Excluding Tax], [Profit], [Quantity Sold]. You may need to analyze data by fiscal year or month ? the calculation has been done through the below query
-	What were the Sales Excluding tax in fiscal year 2015? Base on the below query , the total sales Excluding tax for the fiscal year 2015 is 53827320.95
-	Which fiscal year appears to have the highest profit $? Base on the below query ,2015 is the fiscal year with the highest profit of 26815310.85


```SQL

select
d.[Fiscal Year]  as Fiscal_Year ,
sum(s.[Total Excluding Tax]) as Total_Excluding_Tax,
sum(s.Quantity) as Total_Quantity,
sum(s.Profit) as Total_Profit

from factSale s

join  dimDate d on s.[Invoice Date Key] = d.Date

group by d.[Fiscal Year]

order by Fiscal_Year  DESC

```
What explanation can you offer as to why the profit in 2016 is significantly ; Base on the below query, the profit of the fiscal year 2016 is low compare to the 2015 because, the calculation of the profit of the fiscal year 2016 is computed on only 7 fiscal months whereas that of 2015 is computed on 12 fiscal months.

```SQL

select
d.[Fiscal Year]  as Fiscal_Year ,
d.[Fiscal Month Label] as Fiscal_Month_label,
d.[Fiscal Month Number] as Fiscal_Month_number,
sum(s.[Total Excluding Tax]) as Total_Excluding_Tax,
sum(s.Quantity) as Total_Quantity,
sum(s.Profit) as Total_Profit

from factSale s

join  dimDate d on s.[Invoice Date Key] = d.Date

where [Fiscal Year] in (2015 , 2016)

group by d.[Fiscal Year] , d.[Fiscal Month Label] , d.[Fiscal Month Number] , d.[Calendar Month Number]

order by Fiscal_Year  DESC , Fiscal_Month_number asc

```

Top Selling Products .Sales & Marketing want to know and understand the most valuable products in the current fiscal year 2016.

-	What were the total sales excluding tax in fiscal year 2016? , base on the below query ,the sales excluding tax in fiscal year 2016 is $31,181,195.30


```SQL

d.[Fiscal Year] ,
format(sum(s.[Total Excluding Tax]), 'C') as Total_Excluding_Tax

from factSale s

join dimDate d on s.[Invoice Date Key] = d.Date

where [Fiscal Year] = 2016

group by  d.[Fiscal Year]

```

--What was the top selling product in fiscal 2016 so far? By running the below query , we will have the top 10 selling products in fiscal 2016

```SQL

SELECT TOP(10)
d.[Fiscal Year] ,
I.[Stock Item],
sum(s.[Total Excluding Tax]) as Total_Excluding_Tax

from factSale s

join  dimDate d  on s.[Invoice Date Key] = d.Date

join dimStockItem I on s.[Stock Item Key] = I.[Stock Item Key]

where  [Fiscal Year] = 2016

GROUP BY  d.[Fiscal Year]  , I.[Stock Item]

order BY Total_Excluding_Tax DESC


```

--What was the top performing product / salesperson combination in fiscal 2016? By running the below query ,we have the top  10 performing products / salesperson combination in fiscal 2016

```SQL


SELECT TOP(10)
d.[Fiscal Year] ,
e.[Employee],
p.[Stock Item],
sum(s.[Total Excluding Tax]) as Total_Excluding_Tax

from factSale s

join  dimDate d  on s.[Invoice Date Key] = d.Date

join dimStockItem p on s.[Stock Item Key] = p.[Stock Item Key]

JOIN dimEmployee e on s.[Salesperson Key] = e.[Employee Key]

where  [Fiscal Year] = 2016 and  [Is Salesperson] <> 0

GROUP BY  d.[Fiscal Year]  , p.[Stock Item] , e.[Employee]

order BY Total_Excluding_Tax DESC

```
----What proportion of total fiscal year 2016 sales do these top performances represent? By running the below query , we have the Percentage of Total Sales for the top performances

```SQL


SELECT TOP(10)
d.[Fiscal Year] ,
e.[Employee],
p.[Stock Item],
sum(s.[Total Excluding Tax]) as Total_Excluding_Tax ,

format(cast(sum(s.[Total Excluding Tax]) / (SELECT
                                sum(s.[Total Excluding Tax]) as Total_Excluding_Tax
                                from factSale s
                                join  dimDate d  on s.[Invoice Date Key] = d.Date
                                where  [Fiscal Year] = 2016) as decimal(8,6)),'P') as Percentage_of_Total_Sales

from factSale s

join  dimDate d  on s.[Invoice Date Key] = d.Date

join dimStockItem p on s.[Stock Item Key] = p.[Stock Item Key]

JOIN dimEmployee e on s.[Salesperson Key] = e.[Employee Key]

where  [Fiscal Year] = 2016 and  [Is Salesperson] <> 0

GROUP BY  d.[Fiscal Year]  , p.[Stock Item] , e.[Employee]

order BY Total_Excluding_Tax DESC

```
Think about how you might make your query re-usuable if the current year changes. As seen on the below query, we have to use a subquery combined with the max function  in case we have date greater date than 2016 , for the maximum date to always be retrievable .

```SQL


SELECT TOP(10)
d.[Fiscal Year] ,
e.[Employee],
p.[Stock Item],
sum(s.[Total Excluding Tax]) as Total_Excluding_Tax ,

format(cast(sum(s.[Total Excluding Tax]) / (SELECT
                                sum(s.[Total Excluding Tax]) as Total_Excluding_Tax
                                from factSale s
                                join  dimDate d  on s.[Invoice Date Key] = d.Date
                                where  [Fiscal Year] = (SELECT
                                                        max(d.[Fiscal Year]) as Fiscal_Year
                                                         from factSale s
                                                        join  dimDate d  on s.[Invoice Date Key] = d.Date )) 
                                  as decimal(8,6)),'P') as Percenatge_of_Total_Sales

from factSale s

join  dimDate d  on s.[Invoice Date Key] = d.Date

join dimStockItem p on s.[Stock Item Key] = p.[Stock Item Key]

JOIN dimEmployee e on s.[Salesperson Key] = e.[Employee Key]

where  [Fiscal Year] = (SELECT
                          max(d.[Fiscal Year]) as Fiscal_Year
                           from factSale s
                            join  dimDate d  on s.[Invoice Date Key] = d.Date) 
  and  [Is Salesperson] <> 0

GROUP BY  d.[Fiscal Year]  , p.[Stock Item] , e.[Employee]

order BY Total_Excluding_Tax DESC


```

Top Selling Chiller Product .The business recently introduced chilled products into it’s product line. Produce a table showing individual chiller products, with the quantit. How many chiller products have zero quantity sold to date? base on the below query , we can see that is only the  "White chocolate moon rocks 2kg " item that has zero quantity sold.

```SQL

SELECT 
p.[Stock Item],
sum(s.Quantity) as Quantity,
sum(s.[Total Chiller Items]) as Total_Chille_Items

from  factSale s 

right join  dimStockItem p on  s.[Stock Item Key] = p.[Stock Item Key]

where [Is Chiller Stock] =1

group BY p.[Stock Item] 

order by Quantity desc

```

💻💻  


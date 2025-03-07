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
--How would you best describe the type of customers that we have? 
     
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
 -How many foreign keys does the DimStockItem table have? 

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

### Data analysis
---

``` SQL		
```



``` SQL		
```


### Findings
---

-- On the business over view we are able to dive in the performance by store , category and brand of bed that the company sells.
 
-- On the store  performance overview , we are able to see which day of the week generated the most sale , performance of each category on the business . 
   Also we can assess the performance of each manager responsible for the store at any given period
 

💻💻  


**What are your risk areas? Identify and describe them.**

Answer: while analysing data and try to make it best fit for analysis I have encountered various risk area few of them are described below.

•	The analysis table consists of huge no of rows. But the weird thing is that approximately 60-70% rows are duplicated. To clean the dataset these rows should be deleted but it could be a huge data loss if went wrong.
•	Every table in this dataset contains the information about the product like sku, name. we have to compare the product information between every table and need to standardize the product information. In this process it is an possibility of deleting the product information which could be useful.
•	Connection between the tables are not clear because data entered is inconsistent.
•	Missing values in country and specially city field puts a huge impact on analysis. For example the revenue whish is distributed between different cities of an country could merged into one fake city name that we used to fill that missing value.


**QA Process:**
Describe your QA process and include the SQL queries used to execute it.

Answer: In QA process followed by me for this dataset contains various methods and levels.

•	Eliminating NULL rows from all tables.
	-- all_sessions table
	Query1:
	select * from all_sessions where fullvisitorid is Null(0 rows)
	Explanation:
	Check for the null values if the no. of columns are less we can check all column in one query otherwise it is a best practice to first check for the most important fields whose chances of getting Null is minimum.
	
	--analytics table 
	Query2:
	select * from analytics where visitnumber is Null(0 rows) 
	
	--products table
	Query3:
select * from products
where sku is null and
		name is null and
		orderedquantity is null and
		stocklevel is null and
		restockingleadtime is null and
		sentimentscore is null and
		sentimentmagnitude is null (0rows)

	--sales_by_sku table
	Query4:
select * from sales_by_sku
where productsku is NULL and total_ordered is NULL(0 rows)

--sales_report
	Query5:
select * from sales_report
where productsku is NULL and
		total_ordered is NULL and
		name is NULL and
		stocklevel is NULL and
		restockingleadtime is NULL and
		sentimentscore is NULL and
		sentimentmagnitude is NULL and 
		ratio is NULL	


•	Try to delete duplicate rows from each table. In this process analytics table shows up unexpected results.
	-- select distinct rows from analytics 
	Query:
select distinct(
visitNumber ,
visitId ,
visitStartTime ,
date ,
fullvisitorId ,
userid ,
channelGrouping ,
socialEngagementType,
units_sold,
pageviews,
timeonsite, 
bounces ,
revenue ,
unit_price 
), count(*) from analytics group by (
visitNumber ,
visitId ,
visitStartTime ,
date ,
fullvisitorId ,
userid ,
channelGrouping ,
socialEngagementType,
units_sold,
pageviews,
timeonsite, 
bounces ,
revenue ,
unit_price 
) order by count(*) desc;-- (1739306)rows


select count(*) from analytics; --(4301122)rows




•	Inconsistency in the format of the values entered for prices(Amount).

	I have detected that prices in all fields in all columns are unexpectedly big numbers. Therefore I have followed a standardization to divide it to a specific number to make it more meaningful value. I follow the same procedure for all the tables.

Queries:
•	select productrevenue,count(*) from all_sessions group by productrevenue order by productrevenue::numeric desc;
•	ALTER TABLE all_sessions ADD COLUMN temp_productrevenue numeric;
•	update all_sessions set temp_productrevenue=cast(productrevenue as numeric);
•	select productrevenue,temp_productrevenue from all_sessions
where temp_productrevenue<>productrevenue::numeric; --(0 rows)
•	update all_sessions set temp_productrevenue=round((temp_productrevenue/1000000),2);
•	update all_sessions set temp_productrevenue=0 where temp_productrevenue is NULL;
•	select distinct(temp_productrevenue) from all_sessions;
•	ALTER TABLE all_sessions DROP COLUMN productrevenue; 
•	ALTER TABLE all_sessions RENAME COLUMN temp_productrevenue TO productrevenue;



•	I have seen some invalid data in products table.
Query
		select * from products where orderedquantity>stocklevel

	There are 15 rows present where orderedquantity is greater than the stock available. That shouldn’t be a case so I lower these ordered quantities to the available stock.

	Query:
		select 
		case
			when orderedquantity>stocklevel then stocklevel
			else orderedquantity
		end as orderedquant,stocklevel from products


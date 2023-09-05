Issues:
1.	Checking the rows having all the Null value(in all columns).
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




	----------all sessions table cleaning----------------------------
The datatype of all the columns is set to character varying to minimize the data loss. Now we have to set the datatypes of each column according to the value contained.
2.-- set date column to correct datatype and change the format of the data—
Queries:
•	ALTER table all_sessions ADD COLUMN sessiondate date;
•	update all_sessions set sessiondate=cast(date as date);
•	select date,sessiondate from all_sessions;
•	ALTER TABLE all_sessions DROP COLUMN date; 
•	ALTER TABLE all_sessions RENAME COLUMN sessiondate TO date;



3. I have assume that every price(amount) is a multiple of 1,000,000 therefore I choose to divide the every amount to 1,000,000
	--- totaltransactionrevenue-------------
	Query: 
		select distinct(totaltransactionrevenue) from all_sessions;

	Queries:
•	ALTER TABLE all_sessions ADD COLUMN temp_totaltransactionrevenue numeric;
•	update all_sessions set temp_totaltransactionrevenue=cast(totaltransactionrevenue as numeric);
•	select totaltransactionrevenue,temp_totaltransactionrevenue from all_sessions where temp_totaltransactionrevenue<>totaltransactionrevenue::numeric;
		(0 rows i.e data is copied correctly)
•	update all_sessions set temp_totaltransactionrevenue=round((temp_totaltransactionrevenue/1000000),2);
•	update all_sessions set temp_totaltransactionrevenue=0 where temp_totaltransactionrevenue is NULL;
•	select distinct(temp_totaltransactionrevenue) from all_sessions;
•	ALTER TABLE all_sessions DROP COLUMN totaltransactionrevenue; 
•	ALTER TABLE all_sessions RENAME COLUMN temp_totaltransactionrevenue TO totaltransactionrevenue;
•	select distinct(totaltransactionrevenue) from all_sessions;


4. --transactions----
	Queries:
•	select transactions,count(*) from all_sessions group by transactions;
-- there are only two values Null and 1 so we replace the null to 0 and chage the datatype as smallint
•	update all_sessions set transactions=0 where transactions is null;
•	ALTER TABLE all_sessions ALTER COLUMN transactions TYPE smallint USING transactions::smallint;


5.----timeonsite---
	Queries:
•	select timeonsite,count(*) from all_sessions group by timeonsite order by timeonsite::numeric desc;

-- Here we only change the datatype of the field to numeric and update the NULLs to 0 because the specified type could be in sec or millisecond lets leave it as it is for the timing as value in this format does not change the actual meaning of the column.

•	update all_sessions set timeonsite=0 where timeonsite is null;
•	ALTER TABLE all_sessions ALTER COLUMN timeonsite TYPE int USING timeonsite::int;
6. ----pageviews---
	Queries:
•	select pageviews,count(*) from all_sessions group by pageviews order by pageviews::numeric desc;

	--here we only change the datatype to smallint as it doesnt have null values
•	ALTER TABLE all_sessions ALTER COLUMN pageviews TYPE smallint USING pageviews::smallint;
7. – sessionqualitydim
	Queries:
•	select sessionqualitydim,count(*) from all_sessions group by sessionqualitydim;
•	update all_sessions set sessionqualitydim=0 where sessionqualitydim is null;
•	ALTER TABLE all_sessions ALTER COLUMN sessionqualitydim TYPE smallint USING sessionqualitydim::smallint;
8. –productrefundamount
	Queries:
•	select productrefundamount,count(*) from all_sessions group by productrefundamount;

-- this whole column is null therefore we could neglect this column in our analysis. 
•	ALTER TABLE all_sessions DROP COLUMN productrefundamount;

9. --product quantity
	Queries:
•	select productquantity,count(*) from all_sessions group by productquantity;
•	update all_sessions set productquantity=0 where productquantity is null;
•	ALTER TABLE all_sessions ALTER COLUMN productquantity TYPE smallint USING productquantity::smallint;
10. --productprice----
	Queries:
•	select productprice,count(*) from all_sessions group by productprice order by productprice::numeric desc;
•	
	--- same as that of totaltransactionrevenue

•	ALTER TABLE all_sessions ADD COLUMN temp_productprice numeric;
•	update all_sessions set temp_productprice=cast(productprice as numeric);
•	select productprice,temp_productprice from all_sessions 
		where temp_productprice<>productprice::numeric; --(0 rows)
•	update all_sessions set temp_productprice=round((temp_productprice/1000000),2);
•	select distinct(temp_productprice) from all_sessions;
•	ALTER TABLE all_sessions DROP COLUMN productprice; 
•	ALTER TABLE all_sessions RENAME COLUMN temp_productprice TO productprice;



11. --productrevenue---
	Queries:
•	select productrevenue,count(*) from all_sessions group by productrevenue order by productrevenue::numeric desc;
	--- same as that of totaltransactionrevenue
•	ALTER TABLE all_sessions ADD COLUMN temp_productrevenue numeric;
•	update all_sessions set temp_productrevenue=cast(productrevenue as numeric);
•	select productrevenue,temp_productrevenue from all_sessions
where temp_productrevenue<>productrevenue::numeric; --(0 rows)
•	update all_sessions set temp_productrevenue=round((temp_productrevenue/1000000),2);
•	update all_sessions set temp_productrevenue=0 where temp_productrevenue is NULL;
•	select distinct(temp_productrevenue) from all_sessions;
•	ALTER TABLE all_sessions DROP COLUMN productrevenue; 
•	ALTER TABLE all_sessions RENAME COLUMN temp_productrevenue TO productrevenue;

12. –itemquantity
	Query:
•	select itemquantity,count(*) from all_sessions group by itemquantity order by itemquantity::numeric desc;
	--all the values are NULL. 
•	ALTER TABLE all_sessions DROP COLUMN itemquantity;

13. –itemrevenue
	Query:
•	select itemrevenue,count(*) from all_sessions group by itemrevenue order by itemrevenue::numeric desc;
	--all the values are NULL. 
•	ALTER TABLE all_sessions DROP COLUMN itemrevenue;
13. –searchkeyword
	Query:
•	select distinct(searchkeyword) from all_sessions ;
	--all the values are NULL. 
•	ALTER TABLE all_sessions DROP COLUMN searchkeyword;


14. –transactionrevenue
	Queries:
•	select transactionrevenue,count(*) from all_sessions group by transactionrevenue order by transactionrevenue::numeric desc;
	--- same as that of totaltransactionrevenue
•	ALTER TABLE all_sessions ADD COLUMN temp_transactionrevenue numeric;
•	update all_sessions set temp_transactionrevenue=cast(transactionrevenue as numeric);
•	select transactionrevenue,temp_transactionrevenue from all_sessions where temp_transactionrevenue<>transactionrevenue::numeric; --(0 rows)
•	update all_sessions set temp_transactionrevenue=round((temp_transactionrevenue/1000000),2);
•	update all_sessions set temp_transactionrevenue=0 where temp_transactionrevenue is NULL;
•	select distinct(temp_transactionrevenue) from all_sessions;
•	ALTER TABLE all_sessions DROP COLUMN transactionrevenue; 
•	ALTER TABLE all_sessions RENAME COLUMN temp_transactionrevenue TO transactionrevenue;
15. –transactionid
	Query:
•	select transactionid,count(*) from all_sessions group by transactionid
	--its an alphanumeric column so its ok with character varying.

16. --ecommerceaction_type
	Queries:
•	select ecommerceaction_type,count(*) from all_sessions group by ecommerceaction_type 
	--values looks ok only have to change the datatype as smallint
•	ALTER TABLE all_sessions ALTER COLUMN ecommerceaction_type TYPE smallint USING ecommerceaction_type::smallint;

17. --ecommerceaction_step
	Queries:
•	select ecommerceaction_step,count(*) from all_sessions group by ecommerceaction_step;
	--values looks ok only have to change the datatype as smallint
•	ALTER TABLE all_sessions ALTER COLUMN ecommerceaction_step TYPE smallint USING ecommerceaction_step::smallint;

18. --ecommerceaction_option
•	select ecommerceaction_option,count(*) from all_sessions group by ecommerceaction_option;
--its a character varying field


--analytics table
19. 
•	select * from analytics order by analyticid limit 1000;
	-- only 1000 rows because date is reallt big could not load at once
	Queries:
	--visitnumber
•	select visitnumber,count(*) from analytics group by visitnumber;
•	select analyticid,visitnumber from analytics where visitnumber is null; -- just to check null values
	--change datatype to interger
•	ALTER TABLE analytics ALTER COLUMN visitnumber TYPE smallint USING visitnumber::smallint;

20.—date
	--change datatype and format of date
	Queries:
•	select date,count(*) from analytics group by date;
•	ALTER table analytics ADD COLUMN temp_date date;
•	update analytics set temp_date=cast(date as date);
•	select date,temp_date from analytics limit 1000;
•	ALTER TABLE analytics DROP COLUMN date; 
•	ALTER TABLE analytics RENAME COLUMN temp_date TO date;

21. –visitid
	Queries:
•	select visitid,count(*) from analytics group by visitid;
•	select analyticid,visitid from analytics where visitid is null;-- just to check NULL values
	-- leave this for latter review

22. --userid
•	select userid,count(*) from analytics group by userid;
	-- all the values are null so could be neglected for this data set.
•	ALTER TABLE analytics DROP COLUMN userid


23. --units_sold
	Queries:
•	select units_sold,count(*) from analytics group by units_sold;
	--we assume units could not be negative so we update -ve and null values to 0 and change datatype to int
•	ALTER TABLE analytics ALTER COLUMN units_sold TYPE int USING units_sold::int;
•	update analytics set units_sold=0 where units_sold is null or units_sold<0

24. –pageviews
	Queries:
•	select pageviews,count(*) from analytics group by pageviews order by pageviews::numeric desc;
--we assume pageviews could not null so we update null values to 0 and change datatype to int
•	ALTER TABLE analytics ALTER COLUMN pageviews TYPE int USING pageviews::int;
•	update analytics set pageviews=0 where pageviews is null ;

25. –timeonsite
	Queries:
•	select timeonsite,count(*) from analytics group by timeonsite order by timeonsite::numeric desc;
•	ALTER TABLE analytics ALTER COLUMN timeonsite TYPE int USING timeonsite::int;
•	update analytics set timeonsite=0 where timeonsite is null ;

26. –bounces
	Queries:
•	select bounces,count(*) from analytics group by bounces order by bounces::numeric desc;
	-- we assume bounce could not be null but 0
•	ALTER TABLE analytics ALTER COLUMN bounces TYPE smallint USING bounces::smallint;
•	update analytics set bounces=0 where bounces is null ;

27. –revenue
	Queries:
•	select revenue,count(*) from analytics group by revenue order by revenue::numeric desc;
	--- we have assume that every price(amount) is a multiple of 1,000,000 similar to the all_sessions table
•	ALTER TABLE analytics ADD COLUMN temp_revenue numeric;
•	update analytics set temp_revenue=cast(revenue as numeric);
•	select revenue,temp_revenue from analytics where temp_revenue<>revenue::numeric;-- (0 rows)
•	update analytics set temp_revenue=round((temp_revenue/1000000),2);
•	update analytics set temp_revenue=0 where temp_revenue is NULL;
•	select distinct(temp_revenue) from analytics;
•	ALTER TABLE analytics DROP COLUMN revenue; 
•	ALTER TABLE analytics RENAME COLUMN temp_revenue TO revenue;
•	select distinct(revenue) from analytics;

28. --products table
•	select * from products
	--orderedquantity
	Queries:
•	select orderedquantity,count(*) from products group by orderedquantity order by orderedquantity::numeric desc;
•	select orderedquantity,count(*) from products  where orderedquantity is null group by orderedquantity; --(0 rows)
•	Alter table products alter column orderedquantity type int using orderedquantity::int;

29. –stocklevel
	Queries:
•	select stocklevel,count(*) from products group by stocklevel order by stocklevel::numeric desc;
•	select stocklevel,count(*) from products  where stocklevel is null group by stocklevel; --(0 rows)
•	Alter table products alter column stocklevel type int using stocklevel::int

30. –restockingleadtime
	Queries:
•	select restockingleadtime,count(*) from products group by restockingleadtime order by restockingleadtime::numeric desc;
	--all the values are numeric so we change the datatype to smallint we assume it is an interger 
•	Alter table products alter column restockingleadtime type smallint using restockingleadtime::smallint

31. –sentimentscore
	Queries:
•	select sentimentscore,count(*) from products group by sentimentscore order by sentimentscore::numeric desc;
--we examine that all the values varies around 0 so that could be numeric and also only one value is null 
--therfore we can assign it a 0 value but keep in mind that 0 is not the extream value in column.
•	Alter table products alter column sentimentscore type numeric using sentimentscore::numeric
•	update products set sentimentscore=0 where sentimentscore is null



32. ---sales_by_sku
	Queries:
•	select * from sales_by_sku
	--total_ordered
•	select total_ordered,count(*) from sales_by_sku group by total_ordered order by total_ordered::numeric desc;
•	Alter table sales_by_sku alter column total_ordered type int using total_ordered::int

33. --sales_report
	Queries:
•	select * from sales_report
	--total_ordered
•	select total_ordered,count(*) from sales_report group by total_ordered order by total_ordered::numeric desc;
•	Alter table sales_report alter column total_ordered type int using total_ordered::int

34. –stocklevel
	Queries:
•	select stocklevel,count(*) from sales_report group by stocklevel order by stocklevel::numeric desc;
•	Alter table sales_report alter column stocklevel type int using stocklevel::int

35. –restockingleadtime
	Queries:
•	select restockingleadtime,count(*) from sales_report group by restockingleadtime order by restockingleadtime::numeric desc;
•	Alter table sales_report alter column restockingleadtime type int using restockingleadtime::int

36. –sentimentscore
	Queries:
•	select sentimentscore,count(*) from sales_report group by sentimentscore order by sentimentscore::numeric desc;
•	Alter table sales_report alter column sentimentscore type numeric using sentimentscore::numeric;
•	update sales_report set sentimentscore=0 where sentimentscore is null;

37.—sentimentmagnitude
	Queries:
•	select sentimentmagnitude,count(*) from sales_report group by sentimentmagnitude order by sentimentmagnitude::numeric desc;
•	Alter table sales_report alter column sentimentmagnitude type numeric using sentimentmagnitude::numeric;
•	update sales_report set sentimentmagnitude=0 where sentimentmagnitude is null;


setting primary key for products table:
Query:
•	select count(distinct(sku)) from products (Output: 1092)
•	select count(*) from products (Output: 1092)


both the outputs are same it shows that SKU could be considered as Primary Key for the table Products for this data set.
•	ALTER TABLE products ADD PRIMARY KEY(sku);


check the invalid sku from sales_by_sku table and delete these 
	Queries:
•	select * from sales_by_sku s left join products p on s.productsku=p.sku where p.sku is null; --(8 rows)
	--we can delete these rows
•	DELETE FROM sales_by_sku where productsku in (select s.productsku from sales_by_sku s left join products p on s.productsku=p.sku where p.sku is null)

--comparing sales_by_sku and sales_reports
	Queries:
•	select sp.productsku,sp.total_ordered,s.productsku,s.total_ordered from sales_report sp join sales_by_sku s using(productsku) where sp.productsku<>s.productsku or sp.total_ordered<>s.total_ordered;(0 rows)

--it shows the data in both these tables are duplicate therefore we have to remove duplicacy but lets further explore the data from sales_report table

•	select * from sales_report s left join products p on s.productsku=p.sku where s.name<>p.name or s.stocklevel<>p.stocklevel or s.restockingleadtime<>p.restockingleadtime or s.sentimentscore::numeric<>p.sentimentscore or s.sentimentmagnitude<>p.sentimentmagnitude; -- (0 rows)

-- this query depicts that above mentioned columns in sales_by_sku are duplicated from products table so we have to remove them.

•	ALTER TABLE sales_report
DROP COLUMN name,
DROP COLUMN stocklevel,
DROP COLUMN restockingleadtime,
DROP COLUMN sentimentscore,
DROP COLUMN sentimentmagnitude;

--also this table doesnt have any unique column therefore we will add our own serial field named reportid just for future reference as PRIMARY KEY.

--We find that the table sales_by_sku have same information present in sales_report table. therefore we can delete sales_by_sku table.

•	DROP TABLE sales_by_sku;



Inspect Analytics table

-- select distinct rows from analytics 
Query:
•	select distinct(
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
) order by count(*) desc;-- (1739306)


•	select count(*) from analytics; --(4301122)

--- create a new temp_analytics table
•	CREATE TABLE temp_analytics AS 
TABLE analytics 
WITH NO DATA;
-- insert distinct values into new table
•	INSERT INTO temp_analytics  
	SELECT DISTINCT ON (
	 visitnumber,
	 visitid,
    visitstarttime,
    fullvisitorid ,
    userid ,
    channelgrouping ,
    socialengagementtype ,
    units_sold ,
    pageviews ,
    timeonsite ,
    bounces ,
    date ,
    revenue ,
    unit_price 
		) 
		analyticid, visitnumber,
		visitid,
		visitstarttime,
    		fullvisitorid ,
    		userid ,
    		channelgrouping ,
    		socialengagementtype ,
    		units_sold ,
    		pageviews ,
    		timeonsite ,
    		bounces ,
    		date ,
    		revenue ,
    		unit_price 
		FROM analytics;

•	select * from temp_analytics order by analyticid limit 1000;

-- it shows there are so many rows present in the analytics table therfore we insert the distinct only rows into new table
-- and delete the previous table with duplicate rows. Keep in mind to compare both the tables before deletion.

•	DROP TABLE analytics;
•	ALTER TABLE temp_analytics RENAME TO analytics;
•	select * from analytics order by analyticid limit 1000;



--DELETE rows from all_sessions having invalid productsku
	Queries:
•	select * from all_sessions s
			left join products p on s.productsku=p.sku
			where p.name is null;

	--These are the rows whose product information is not present in products table. We assume products table as the standared table for the products
	--therefore we can delete these rows.


•	DELETE FROM all_sessions
where productsku in (
select s.productsku from all_sessions s
left join products p on s.productsku=p.sku
where p.name is null
)


--set the foreign key
--We assume productsku in all_sessions table and sales_report table is the foreign key from products table primary key column SKU
Queries:
•	ALTER TABLE all_sessions
ADD CONSTRAINT constraint_fk
FOREIGN KEY (productsku)
REFERENCES products(sku);

•	ALTER TABLE sales_report
ADD CONSTRAINT constraint_fk
FOREIGN KEY (productsku)
REFERENCES products(sku);

--try to set the missing city values
	Queries:
•	select country,city from all_sessions where city like 'not available in demo dataset'
•	select country,city from all_sessions where city like '(not set)'
--here we are going to copy the country name to the missing city values
•	update all_sessions set city=country where city like 'not available in demo dataset' or city like '(not set)'	
	


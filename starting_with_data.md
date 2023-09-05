Question 1: --Exploring the effective channel

SQL Queries:
•	select distinct(channelgrouping),count(*) from all_sessions group by channelgrouping order by count(*) desc;

Answer: 
	The output from the above query could be used to differentiate between the type of sources used to visit the website and helps to determine which channel is most effective and which one is least.



Question 2: --finding the average no of visits per day
SQL Queries:
•	with cte_visits as(
select distinct(date),count(*) from all_sessions group by date order by count(*) desc
		)select avg(count)::integer from cte_visits
Answer:
	
	Avg: 36



Question 3: --find which visitor have max no of bounces

SQL Queries: 
•	select distinct(fullvisitorid),sum(bounces) as noofbounces from analytics group by fullvisitorid order by sum(bounces) desc limit 1;

Answer:

"fullvisitorid"		"noofbounces"
"4215347458239853405"	440



Question 4: -- find the product having maximum stock.
SQL Queries:
•	select sku,name,stocklevel from products order by stocklevel desc limit 1
Answer:

	"sku"			"name"				"stocklevel"
	"GGOEGDHC018299"	" 22 oz Water Bottle"		19678




Question 5: --which product have maximum product revenue
SQL Queries:
•	select distinct(productsku),sum(productrevenue) as product_revenue from all_sessions group by productsku order by sum(productrevenue) desc limit 1;

Answer:
"productsku"		"product_revenue"
"GGOENEBB078899"	120.00

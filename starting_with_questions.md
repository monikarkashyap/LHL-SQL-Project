Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
•	select country,city,transactionrevenue from all_sessions order by transactionrevenue desc limit 1;


Answer:

"country"	       "city"			"transactionrevenue"
"United States"		"United States"		1005.50

Here city is same as that of country because city column had some invalid and missing values therefore to fill the missing values I have to copy the corresponding country to the city value.
I have assume that every price(amount) is a multiple of 1,000,000 therefore I choose to divide the every amount to 1,000,000
So that value 1005.50 could be (1,005,500,000) in the original dataset.



**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

•	with cte_noofproducts as (
select city,country,count(productsku) as no_of_products from all_sessions 
		group by city,country
		)
select avg(no_of_products)::int as average_products from cte_noofproducts;
Answer:
	"average_products"	: 33

Since the no. of products could not be in fractions therefore we cast it to whole number.


**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

	--if we look at the product category it shows it contains information about the categories and subcategories.
•	select distinct(v2productcategory),count(*) from all_sessions group by v2productcategory order by count(*) desc

This query depicts that there are some categories which are most popular among visitors and some are least. 
Figuring out least is difficult as there are so many in least list but we can easily 
figure out top three or four most popular categories.
output: 
"v2productcategory"			"count"
"Home/Shop by Brand/YouTube/"		2240
"Home/Apparel/Men's/Men's-T-Shirts/"	1761
"Home/Apparel/"				765
"Home/Electronics/"			688

If we want to analyse visitors from which countries and cities shops from which category the most. Can be done using:-
Query:
•	select distinct(country,city,v2productcategory),count(*) from all_sessions group by country,city,v2productcategory 
		order by count(*) desc

"row"																		  "count"
"United States","United States",
			"Home/Shop by Brand/YouTube/"		:568
"United States","United States",
		”Home/Apparel/Men's/Men's-T-Shirts/"		:391
"United States","United States",
		”Home/Apparel/"					:259
"United States","United States",
		”Home/Electronics/"				:224

country and city is same as we know we have filled the missing cities.




**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
•	with cte_product_rank as(
select DISTINCT ON (country,city,productsku)
	country,city,productsku
	,count(*) as countp
from all_sessions group by country,city,productsku 
),cte_top_selling as(
select country,city,productsku,countp from cte_product_rank c
where countp=(select max(countp) from cte_product_rank c1 where c1.country=c.country and c1.city=c.city )
)
select * from cte_top_selling;


Answer:

	By examining the output from the above query it shows that multiple products share the top selling product status in each country/city therefore its not worth  having to take further this analysis.



**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

•	select distinct(country,city),sum(totaltransactionrevenue) as ts,sum(transactionrevenue) s from all_sessions
group by country,city having sum(totaltransactionrevenue)<>0 or sum(transactionrevenue) <>0
order by ts desc

Answer:

The output from above query shows that only few cities and countries participate in generating the revenue.









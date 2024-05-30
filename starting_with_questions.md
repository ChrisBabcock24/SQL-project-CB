Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
 **Query to retrieve both country and city and ORDER BY highest transaction revenue.**
**I created an additional table with identical columns and filtered it for only rows where transactions have been made then used this to query**
**It has been named all_sessions_transactions**
SELECT 
    country,
    city,
    sum(total_transaction_revenue) AS total_transaction
FROM
    all_sessions_transactions
GROUP BY
    country,
    city
ORDER BY
    total_transaction DESC
LIMIT 1 **returns only the top transaction result**

Answer:

"United States"	"not available in demo dataset"	6092.5599999999995

 **When running this query it gave a result that was not useful due to the fact that there was not a valid city.**
 **A modification to the code could include a HAVING statement to filter out any city that has the value 'not available in demo dataset.'**

**That query would be:**
SELECT 
    country,
    city,
    sum(total_transaction_revenue) AS total_transaction
FROM
    all_sessions_transactions
GROUP BY
    country,
    city
HAVING
    city != 'not available in demo dataset'
ORDER BY
    total_transaction DESC
LIMIT 1

**Answer : "United States" "San Francisco"	1564.32**


**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

SELECT	
	full_visitor_id,
	product_sku,
	city,
	country,
	ROUND(AVG(product_quantity),2) AS avg_product
FROM
	all_sessions_transactions
WHERE city !='not available in demo dataset'
GROUP BY
full_visitor_id,
product_sku,
	country,
	city
	
HAVING 
	AVG(product_quantity) IS NOT NULL 
ORDER BY
 avg_product DESC


Answer:

**All returns were the same (1.0) except for Atlanta (4.0). There were limited values in the 'product_quantity' column in the 'all_sessions'**
**table and 'item_quantity' was equally void of values**
**The sales_report and 'sales_by_sku' tables had columns for 'total_ordered' but could not associate with individual visitors without more context**
**ROUNDed the AVG result to remove excess 0's, added WHERE condition to filter out any results that had city as 'not available in demo dataset'**
**Additional HAVING clause to filter out any average result that is NULL**

"8444725132150789814"	"GGOEGBJR018199"	"Atlanta"	"United States"	    4.00
"8782850138494385516"	"GGOENEBB078899"	"Seattle"	"United States"	    1.00
"8126668011547441685"	"GGOENEBJ079499"	"New York"	"United States"	    1.00
"1250309821000380040"	"GGOEGAAJ032617"	"Columbus"	"United States"	    1.00
"2307679166084738265"	"GGOENEBJ079499"	"Mountain View"	"United States"	1.00
"0395821106338763980"	"GGOENEBJ079499"	"Palo Alto"	"United States"	    1.00
"7871129310022840984"	"GGOEGAAX0794"	"San Francisco"	"United States"	    1.00
"5961512277421237379"	"GGOEGAAJ080616"	"Mountain View"	"United States"	1.00
"537940071228449977"	"GGOEGHGR019499"	"Chicago"	"United States"	    1.00
"9692588428659286117"	"GGOEGAAX0104"	"New York"	"United States"	        1.00
"25788925527919759"	"GGOENEBQ078999"	"San Francisco"	"United States" 	1.00
"4396927063023774578"	"GGOENEBB078899"	"Sunnyvale"	"United States"	    1.00



**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
**Query that counts the instances of product categories by country and city and returns Highest to Lowest**

SELECT
    v2_product_category,
    COUNT(v2_product_category) AS product_hits,
    country,
    city
FROM
	all_sessions_transactions
GROUP BY
    v2_product_category,
country,
    city
ORDER BY
    product_hits DESC

Answer:

**The product categories are counted by each country and city.**
**You can interpret this data result to give you an idea as to where you are selling the most of one item**
**You can then focus on areas to improve your sales**
**Kept cities that had values "not available in demo dataset" to show a greater spread of number of products in return**
**Would need more information to change the city values to an actual city**

"Home/Nest/Nest-USA/"	                    8	"United States"	"not available in demo dataset"
"Home/Nest/Nest-USA/"	                    4	"United States"	"San Francisco"
"Home/Nest/Nest-USA/"	                    3	"United States"	"Palo Alto"
"Electronics"	                            2	"United States"	"not available in demo dataset"
"Home/Nest/Nest-USA/"	                    2	"United States"	"Mountain View"
"Home/Nest/Nest-USA/"	                    2	"United States"	"Sunnyvale"
"Apparel"	                                2	"United States"	"Mountain View"
"Home/Apparel/Women's/Women's-T-Shirts/"	2	"United States"	"not available in demo dataset"
"Home/Apparel/Men's/Men's-T-Shirts/"	    2	"United States"	"not available in demo dataset"
"Waze"	                                    1	"United States"	"Mountain View"
"Home/Bags/"	                            1	"United States"	"not available in demo dataset"
"Apparel"	                                1	"United States"	"Austin"
...............................................................................................
**MORE RETURNS in actual query**


**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
**query that references a subquery  to find the top selling products by country and city**
**(calculates the rank of the product partitioning by country and city, then calculates the total within the grouping and sets a rank)**
**the outer where condition ensures that only the top selling from each group is returned**

SELECT 
	country, 
	city, 
	v2_product_name AS top_seller,
	rank
FROM
	(SELECT 
		country,
		city,
		v2_product_name,
		DENSE_RANK () OVER (PARTITION BY country, city ORDER BY SUM(total_transaction_revenue) DESC) AS rank
	FROM
		all_sessions_transactions
	GROUP BY
		country, city, v2_product_name) AS product_ranks
WHERE 
	RANK = 1
ORDER BY 
	top_seller


Answer:

**We can interpret the data to see that there are quite a few instances of NEST devices indicating a demand for smarthome products**


"United States"	"Houston"	"26 oz Double Wall Insulated Bottle"	1
"USA"	"New York"	"Google Men's  Zip Hoodie"	1
"United States"	"Austin"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	1
"Canada"	"Toronto"	"Google Men's 3/4 Sleeve Raglan Henley Grey"	1
"United States"	"Columbus"	"Google Men's Short Sleeve Badge Tee Charcoal"	1
"United States"	"San Bruno"	"Google Men's Vintage Badge Tee White"	1
"Australia"	"Sydney"	"Nest® Cam Indoor Security Camera - USA"	1
"United States"	"Seattle"	"Nest® Cam Indoor Security Camera - USA"	1
"United States"	"San Jose"	"Nest® Cam Outdoor Security Camera - USA"	1
"United States"	"Palo Alto"	"Nest® Cam Outdoor Security Camera - USA"	1
"United States"	"Los Angeles"	"Nest® Cam Outdoor Security Camera - USA"	1
"United States"	"Mountain View"	"Nest® Learning Thermostat 3rd Gen-USA"	1
"United States"	"Chicago"	"Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel"	1
"United States"	"Nashville"	"Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel"	1
"United States"	"New York"	"Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel"	1
"United States"	"San Francisco"	"Nest® Protect Smoke + CO Black Battery Alarm-USA"	1
"Israel"	"Tel Aviv-Yafo"	"Nest® Protect Smoke + CO Black Wired Alarm-USA"	1
"United States"	"not available in demo dataset"	"Reusable Shopping Bag"	1
"United States"	"Atlanta"	"Reusable Shopping Bag"	1
"United States"	"Sunnyvale"	"SPF-15 Slim & Slender Lip Balm"	1
"Switzerland"	"Zurich"	"YouTube Men's 3/4 Sleeve Henley"	1





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

**Query to find the total revenue of each city and within each country and then Order it by revenue**
SELECT
	country,
	city,
	ROUND(SUM(total_transaction_revenue)::NUMERIC,2) AS total_revenue
FROM
	all_sessions_transactions
GROUP BY
	country,
	city
ORDER BY
	total_revenue DESC

Answer:

**This return can give insight into locations  that are driving the sales as well as locations that have not performed well**
**It appears that the United States is the major consumer of the products in the database**

"United States"	"not available in demo dataset"	6092.56
"United States"	"San Francisco"	1564.32
"United States"	"Sunnyvale"	992.23
"United States"	"Atlanta"	854.44
"United States"	"Palo Alto"	608.00
"Israel"	"Tel Aviv-Yafo"	602.00
"United States"	"New York"	530.36
"United States"	"Mountain View"	483.36
"United States"	"Los Angeles"	479.48
"United States"	"Chicago"	449.52
"Australia"	"Sydney"	358.00
"United States"	"Seattle"	358.00
"United States"	"San Jose"	262.38
"United States"	"Austin"	157.78
"United States"	"Nashville"	157.00
"United States"	"San Bruno"	103.77
"Canada"	"Toronto"	82.16
"USA"	"New York"	67.99
"United States"	"Houston"	38.98
"United States"	"Columbus"	21.99
"Switzerland"	"Zurich"	16.99






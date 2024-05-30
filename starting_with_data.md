Question 1: What categories are the most popular


SQL Queries:

SELECT 
	DISTINCT(v2_product_category),
	COUNT(*)
FROM
	all_sessions_transactions
GROUP BY
	DISTINCT(v2_product_category)
ORDER BY
	COUNT(*) DESC
LIMIT 1

Answer: 

"Home/Nest/Nest-USA/"	27


Question 2: what is the average time spent on site vs the revenue gained

SQL Queries:

SELECT 
	AVG(time_on_site) AS avg_time_on_site, 
	AVG(revenue) AS revenue_avg
FROM
	analytics_transactions

Answer:
**calculated the averages of time and revenue to see how long it takes to return a purchase or sale.**
avg_time_on_site          revenue_avg
1114.2547717296595262	391.03100127017734



Question 3:  What channel group results in the most revenue

SQL Queries:
SELECT 
	channel_grouping, 
	ROUND(SUM(revenue)::NUMERIC,2) AS revenue_total
FROM 
	analytics_transactions
GROUP BY 
	channel_grouping
ORDER BY 
revenue_total DESC

Answer:
**Query shows all groups for reference**
**Can include LIMIT to only show the desired amount ie: top 3, only the very top**
1. "Referral"	10506951.73


Question 4: 

SQL Queries:

Answer:



Question 5: 

SQL Queries:

Answer:

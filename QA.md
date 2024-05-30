What are your risk areas? Identify and describe them.

Risk areas included columns that had large amounts of NULL values, this could possibly skew the results of data when calculating if the row is counted in
results.

Formatting of data fields with names - Some fields in the product names had similar names but spelling or capitalization was slightly different.
Multiple names for the same item may also give a false result if is it grouped correctly.

Conflicting values or data types - calculations could return as NULL if not in correct data type

Formatting of data fields with names - Some fields in the product names had similar names but spelling or capitalization was slightly different.

I found that if a query was run more than once while using the UPDATE statement that the data would be invalid as it calculated on the previously UPDATED
value. Use SELECT to run these queries before you are absolutely sure you want to UPDATE table. 

QA Process:
Describe your QA process and include the SQL queries used to execute it.

When exploring the dataset and creating queries I would test my results by comparing the amount of rows returned for a query without a condition with the
result of the query with a condition present. When I was confident in my query I would test right after before continuing or changing table values.
I used this when eliminating duplicates. I would SELECT all the columns in the table where the ROW_NUMBER was greater than >1 to ensure I had chosen the correct rows, and that the rows I wanted kept were left out of SELECT statememnt. Only then would I proceed to DELETE

-- checking to see if units sold and revenue both have values
SELECT *
FROM
	public.analytics_temp
WHERE
	units_sold >1 and revenue IS NOT NULL

 -- calculate the revenue when revenue field is NULL, if both are NULL THEN OUTPUT 0 using coalesce
--if revenue has a value then keep that value

WITH revenue_calc_CTE AS(
SELECT 
	*,
	CASE WHEN 
		revenue IS NULL THEN 
			coalesce(units_sold * unit_price,0) 
			ELSE
				revenue
		END AS revenue
FROM
	analytics_temp2)
	
UPDATE analytics_temp2
SET revenue = CASE WHEN 
		revenue IS NULL THEN 
			coalesce(units_sold * unit_price,0) 
			ELSE
				revenue
		END;
**This last query checks to ensure the update was successful and altered any NULL to the calculated result.**
SELECT* FROM public.analytics_temp2
WHERE revenue IS NULL   

**This checked to see if unit_sold coincided with a revenue value. Checking to confirm a transaction took place.** 
SELECT *
FROM
	analytics
WHERE
	units_sold > 0 and revenue IS NOT NULL

**This was a situation where I had a potential duplicate**
**I found a duplicate and created another query to find all rows where the visit_id and unit_price equalled the potential duplicate.**
**determined there were duplicates as all of the information in the rows matched eachother**
**Using queries PARTITIONING just on visit_number and full_visitor_id I had found situations where some fields were identical but the unit price was different meaning that row was not a duplicate**
WITH duplicate_analytics_CTE AS
(SELECT 
 	*,
ROW_NUMBER () OVER (PARTITION BY visit_number, visit_id, visit_start_time, date,
					full_visitor_id, user_id, channel_grouping, social_engagement_type,
					units_sold, page_views, time_on_site, bounces, revenue, unit_price) AS row_num
FROM
 	analytics_temp)
	
SELECT 
 	*
FROM
 	duplicate_analytics_CTE
WHERE
	row_num >1
ORDER BY
	row_num;
-- found duplicates	

SELECT *
FROM
	analytics_temp
WHERE
visit_id = '1493622532' AND unit_price = 16990000


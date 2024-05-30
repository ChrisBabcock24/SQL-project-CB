What issues will you address by cleaning the data?

1. Compile all product_skus into one column using joins
2. Change time and date fields to correct format
3. Clean duplicates
4. Check for NULL or irrelevent data
5. Filter the completed transactions from the incomplete 
6. Correct formatting.
7. Convert transactions to a boolean column where NULL = FALSE(no transaction made) 
and then use this to filter. Questions are concerned with successful transactions
8. Convert revenue, unit_price and other fields that are associated with monetary values. 
The numbers appear to be multiplied by 1 million.
9. Determining a way to create primary keys for tables all_sessions and analytics, no clear column had all unique values
10. Calculate missing revenue fields using units_sold and unit_price. 
Confirming by referencing existing rows with data present and comparing the calc of their respective units_sold and unit_price
11. Found an instance of incorrect country in relation to city CANADA - NEW YORK
12. During some querying I noticed certain products had multiple similar names, converted to one name to group by
13.

Queries:
Below, provide the SQL queries you used to clean your data.

1. -- COMPILING PRODUCT_SKUS INTO 1 TABLE WITH NAMES
CREATE TABLE unique_skus (product_sku text, product_name text);

INSERT INTO unique_skus (product_sku)

SELECT product_sku FROM(
	SELECT product_sku 
	FROM public.products
UNION
	SELECT product_sku 
	FROM public.sales_report
UNION
	SELECT product_sku
	FROM public.sales_by_sku
UNION
	SELECT product_sku
	FROM public.all_sessions
	) AS combined_skus;

**Then JOINED all tables that had product names, added names to 'unique_sku' using a CASE STATEMENT when product_sku's matched.**
**CTE was used to CREATE a list of all product_skus in unique_skus and  JOIN to table with associated names.**

-- compiling all names associated with skus
WITH compile_names_CTE AS(
SELECT
	us.product_sku AS product_sku,
	p.product_name AS name_products,
	sr.product_name AS name_sales_report,
	als.v2_product_name AS name_all_sessions
	
FROM
	unique_skus us
LEFT JOIN
	products p
ON
	us.product_sku = p.product_sku
LEFT JOIN
	sales_report sr
ON
	p.product_sku = sr.product_sku
LEFT JOIN
	all_sessions als
ON
	sr.product_sku = als.product_sku)

--updating the names using the CTE
UPDATE unique_skus us
SET product_name = 	
	CASE WHEN 
		us.product_sku = cnC.product_sku THEN cnC.name_products
	END
FROM
	compile_names_CTE cnC

WHERE us.product_sku = cnC.product_sku


2. **required multiple attempts for the visit_date column to accept a conversion. Used a query to find fields that were not NULL**
   **Checks for a pattern containing 8 digits and its ability to be converted into a date from string.**
   **If the digit does not match then it changes it to NULL**

--WORKING CONVERSION TO DATE - SEARCHES FOR 8  NUMERIC DIGITS AND NOT NULL THEN CONVERTS TO DATE FORMAT OTHERWISE CHANGES TO NULL

UPDATE all_sessions_temp
SET visit_date = CASE 
			WHEN visit_date ~ '^[0-9]{8}$' AND TO_DATE(visit_date, 'YYYYMMDD') IS NOT NULL THEN TO_DATE(visit_date, 'YYYYMMDD')
			ELSE NULL
			END;


3. **Used the ROW_NUMBER to PARTITION by ALL rows - this creates a row number. If the row is unique it will have a value of 1.**
**When there are duplicates the row_number will be higher --- 2 or more. I filtered the result having row_number > 1 to give me a list of duplicate rows.** 
**then DELETED using that filter.**

SELECT *,	
	ROW_NUMBER() OVER (PARTITION BY visit_number, visit_id, visit_start_time,
					   visit_date, full_visitor_id, user_id, channel_grouping, 
					   social_engagement_type, units_sold, page_views, 
					   time_on_site, bounces, revenue, unit_price) AS row_num	
FROM
	analytics
**Made the above into a CTE and then inserted the resulting row_num into the table**
**Then deleted all values for row_num that had more than one count**
delete
FROM analytics
WHERE row_num >1

4. 
SELECT 
    *
FROM 
    analytics
WHERE 
    units_sold IS NOT NULL

5. **This would return the transactions when true**
   **can switch the condition to false and retrieve the transactions that were not successful**
SELECT
	*
FROM
	all_sessions_temp
WHERE
	transactions is TRUE



6. **required changing from text to float in order to use in calculations**
ALTER TABLE 
    all_sessions_temp
ALTER COLUMN 
    total_transaction_revenue TYPE FLOAT USING total_transaction_revenue::FLOAT

7. **created temporary table to prevent data being corrupt if the query did not return correct information**
UPDATE 
    all_sessions_temp
SET transactions =
	CASE 
		WHEN transactions IS NULL THEN 'FALSE'
		WHEN transactions = '1' THEN 'TRUE'
		
	END;

**Then converted to BOOLEAN**
ALTER TABLE all_sessions_temp
	ALTER COLUMN transactions TYPE BOOLEAN USING transactions::boolean

8. **converted multiple columns using this query. changed the corresponding column name in the calculated area**
SELECT (unit_price/1000000)::float AS corrected_unit_price
FROM
	analytics


9. **Once duplicates were removed from table, created a basic row_number column and used this as a key.**
**I had previously eliminated all duplicates by PARTITIONING by all columns so each row is unique**
**I then set a row_number for each row**
UPDATE analytics_transactions
SET row_number_column = ROW_NUMBER() OVER ()

10. -- calculate the revenue when revenue field is NULL, if both are NULL THEN OUTPUT 0 using coalesce
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
	analytics_transactions)
	
UPDATE analytics_transactions
    SET revenue = CASE WHEN 
		    revenue IS NULL THEN 
			    coalesce(units_sold * unit_price,0) 
			ELSE
				revenue
		END;

11. **FOUND AN INCORRECT COUNTRY COMPARED TO CITY**
SELECT
	*
FROM
	all_sessions_temp2
WHERE 
	country = 'Canada' AND city = 'New York'
	
-- change the value from CANADA TO USA
UPDATE all_sessions_temp2
SET country = 'USA'
WHERE 
	country = 'Canada' AND city = 'New York';

12. **Changed any category that included Nest within the string to group more accurately**
UPDATE all_sessions_transactions
SET v2_product_category = 'Home/Nest/Nest-USA/'
WHERE v2_product_category ILIKE '%nest%';

13. **Changed the format from intial text to numeric to be able to calculate average time spent on site**
**Time interval could not be assumed in this column so left as numeric**
ALTER TABLE analytics_transactions
ALTER COLUMN time_on_site TYPE NUMERIC USING time_on_site::NUMERIC




# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
My  goal for this project was to implement my understanding of SQL queries and 
practice cleaning data. I hoped to clean the dataset as accurately as possible within the timeframe
I also intended to identify relationships between tables to be able to fill in
missing information in certain columns.

## Process
1. First step was to input the main tables into database.
Used data type text to maintain data integrity before changing to 
appropriate data types.

2. There are similar names within each table. Changed titles of all columns to a uniform format. Confirmed that these columns had matching values
When initially querying, the capitalization of certain column names created errors when attempting 
to retrieve in SELECT statements.

3. Spent some time observing the values within rows and possible ways to match with other columns

4. Converted data types once I understood the context of 
the column. Some columns related to time were not easily identifiable.

5. Created additional tables and copied data from original table.
This was to ensure that I did not potentially lose data when performing exploratory querying.

6. Compared rows in their entirety (partitioned by all columns) to find any duplicate entries.
Any duplicates were then deleted. This was performed on the additional
columns only to maintain the original data. 

7. Determined which rows resulted in a transaction and filtered the results into new tables
for all_sessions and analytics. 

8. Tried to find suitable columns to use for PRIMARY KEY
If there were no clear candidates for primary key, I created 
a ROW_NUMBER and PARTITIONED by all columns to find all unique
entries.

9. Found that some product_sku's were in some tables but not others.
  used JOINS to merge all known product_sku's into one table with appropriate
product_name.
This was also the case with full_visitor_id. Merged them into one table for reference.


## Results
(fill in what you discovered this data could tell you and how you used the data to answer those questions)

I discovered that the database could provide insights regarding time spent on the site vs the amounts purchased.

SELECT 
    full_visitor_id,
    SUM(time_on_site) AS total_time_on_site,
    ROUND(SUM(revenue)::NUMERIC,2) AS total_revenue
FROM 
    analytics_transactions
GROUP BY 
    full_visitor_id
ORDER BY
	total_revenue DESC

  This would aggregate the totals for time on site and revenue grouped by each full_visitor_id.

  The amounts of products sold could be totalled and grouped by location to provide a glimpse into how effective the site is
in various regions, cities etc.

SELECT 
  country,
  city,
  product_sku,
	SUM(total_transaction_revenue) AS total_revenue   	
FROM 
  all_sessions_transactions
GROUP BY 
  country, 
	city,
	product_sku
ORDER BY 
  total_revenue DESC

This would return, the product_sku and product_name grouped by country and city, then sorted by total revenue with the highest revenue as first.


## Challenges 
(discuss challenges you faced in the project)

1. Finding suitable PRIMARY KEY candidates for all_sessions and analytics was difficult
and took a lot of time exploring.
This made comparing similar data from the two tables difficult.

2. The analytics table did not have locations associated with data but contained the most row entries (most data to interpret).

3. There were multiple full_visitor_id's in all_sessions table with unique product prices. Finding a way to relate the rows and compare to analytics table is proving difficult. 

4. Query times became very long when dealing with the larger tables

5. Not corrupting data when exploring using queries like altering values if converting from one data type to another.

## Future Goals
(what would you do if you had more time?)

If I had more time:

- I would have liked to find better datatypes for the text columns and continue to identify the right type to use. 
- I'd like to calculate missing values in some columns regarding transactions. This would greatly improve the accuracy of the queries and results to be analyzed
- I'd like to match the analytics_transactions table with a country and city. There is a substantial amount of information in this table that presently cannot be associated to a region or city.
- I would like to find  out which columns are not relevant or do not provide valuable information
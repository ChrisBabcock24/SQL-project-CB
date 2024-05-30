# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
My  goal for this project was to implement my undderstanding of SQL queries and 
practice cleaning data. I hoped to clean the dataset as accurately as possible.
I also intended to identify relationships between tables to be able to fill in
missing information in certain columns.

## Process
1. First step was to input the main tables into database.
Used data type text to maintain data integrity before changing to 
appropriate data types.

2.There are similar names within each table. Changed titles of all columns to a uniform format .
When initially querying the capitalization of certain column names created errors when attempting 
to retrieve in SELECT statements.

3. Spent some time observing the values within rows and possible ways to match with other columns

4. Converted data types once I understood the context of 
the column. Some columns related to time were not easily identifiable.

5. Created additional tables and copied data from original table.
This was to ensure that I did not potentially lose data when performing exploratory querying.

6.Compared rows in their entirety to find any duplicate entries.
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


## Challenges 
(discuss challenges you faced in the project)
Finding suitable PRIMARY KEY candidates for all_sessions and analytics was difficult
and took a lot of time exploring.
This made comparing similar data from the two tables difficult.
The analytics table did not have locations associated with data hut contain the most
row entries.
there were multiple full_visitor_id's in all_sessions table with unique product prices.
could not compare to analytics table using only full_visitor_id.


## Future Goals
(what would you do if you had more time?)

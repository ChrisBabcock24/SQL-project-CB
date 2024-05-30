What issues will you address by cleaning the data?

1.Compile all product_skus into one column using joins
2.Change time and date fields to correct format
3.Clean duplicates
4.Check for NULL or irrelevent data
5.Filter the completed transactions from the incomplete 
6 Correct formatting where data types are known.
7. Convert transactions to a boolean column where NULL = FALSE(no transaction made) 
and then use this to filter. Questions are concerned with successful transactions
8.Convert revenue, unit_price and other fields that are associated with monetary values. 
The numbers appear to be multiplied by 1 million.
9. Determining a way to create primary keys for tables all_sessions and analytics, no clear column had all unique values
10.Calculate missing revenue fields using units_sold and unit_price. 
Confirming by referencing existing rows with data present and comparing the calc of their respective units_sold and unit_price
11. Found an instance of incorrect country in relation to city CANADA - NEW YORK

Queries:
Below, provide the SQL queries you used to clean your data.

**Changed any category that included Nest within the string to group more accurately**
UPDATE all_sessions_transactions
SET v2_product_category = 'Home/Nest/Nest-USA/'
WHERE v2_product_category ILIKE '%nest%';

**Changed the format from intial text to numeric to be able to calculate average time spent on site**
**Time interval could not be assumed in this column so left as numeric**
ALTER TABLE analytics_transactions
ALTER COLUMN time_on_site TYPE NUMERIC USING time_on_site::NUMERIC


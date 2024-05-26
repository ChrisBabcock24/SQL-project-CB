What issues will you address by cleaning the data?

1.COMPILING SKUS INTO ONE TABLE

The sales_report and sales_by_sku tables have columns that share most of the same information.
The sales_by_sku table has a few additional SKU numbers without names associated.

I will be removing duplicate entries in the sales_report and sales_by_sku tables by using LEFT JOIN
to keep the unnamed skus until they can either be identified or removed if not relevant.

'products' table contains more sku's than 'sales_report' and 'sales_by_sku' tables
and the 'all_sessions' contains even more skus.

I will be compiling all of the distinct skus into one table to reference and relate to other tables

2. removing duplicates from anayltics
created temporary tables to clean data 
converted unit_price to corrected values ( /1000000) - this was done on multiple columns within all_sessions and analytics

3.converted transaction null into a 0 and then True or False (boolean) - then filtered on this field to determine if transaction was made. confirmed by selecting both revenue and units_sold to make sure that transaction field was accurately reflecting a sale.
4. converted time related columns to more recognizable formats.

5. calculated revenue based on other columns to fill in missing data in revenue column

6. 
removed rows from temporary table that did not result in a transaction
Queries:
Below, provide the SQL queries you used to clean your data.


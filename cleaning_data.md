What issues will you address by cleaning the data?

COMPILING SKUS INTO ONE TABLE
1.The sales_report and sales_by_sku tables have columns that share most of the same information.
The sales_by_sku table has a few additional SKU numbers without names associated.
I will be removing duplicate entries in the sales_report and sales_by_sku tables by using LEFT JOIN
to keep the unnamed skus until they can either be identified or removed if not relevant.
products table contains more sku's than sales_report and sales_by_sku tables
and the all_sessions contains even more skus.
I will be compiling all of the distinct skus into one table to reference and relate to other tables

2. removing duplicates from anayltics
created temporary tables to clean data 
converted unit_price to corrected values ( /1000000)

Queries:
Below, provide the SQL queries you used to clean your data.

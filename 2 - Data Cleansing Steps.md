## 1.	Data Cleaning steps
In a single query, perform the following operations and generate a new table in the data_mart schema named clean_weekly_sales:

•	Convert the week_date to a DATE format

•	Add a week_number as the second column for each week_date value, for example any value from the 1st of January to 7th of January will be 1, 8th to 14th will be 2 etc

•	Add a month_number with the calendar month for each week_date value as the 3rd column

•	Add a calendar_year column as the 4th column containing either 2018, 2019 or 2020 values

•	Add a new column called age_band after the original segment column using the following mapping on the number inside the segment value

![age_band](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/dfc45bf1-3bd3-46a4-90fd-3e0c863ac4fc)



•	Add a new demographic column using the following mapping for the first letter in the segment values

![segment](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/8255ba6b-c3fa-4533-86a0-836c08f565c3)


•	Ensure all null string values with an "unknown" string value in the original segment column as well as the new age_band and demographic columns
•	Generate a new avg_transaction column as the sales value divided by transactions rounded to 2 decimal places for each record

## Task 1: convert the week_date to a DATE format
**SQL Query to check `DATE` Format**
```sql

SELECT *
FROM data_mart.weekly_sales;

```
![Screenshot 2023-10-21 211546](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/e995c3ea-5b4a-47da-85bb-557f94c76230)


 **Convert `week_date` column to Date**
```sql
SELECT
      week_date :: date
FROM data_mart.weekly_sales;
```
![week_date date](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/58e2c94a-df00-4299-ba5b-9eab3e37a896)


**Extracting Week, Month, and Year Information from `week_date`**

```sql
-- Add `week_number` as the second column, `month_number` as the third column, and `calendar_year` as the fourth column
SELECT 
    week_date::DATE,
    EXTRACT(WEEK FROM week_date::DATE) AS week_number,
    EXTRACT(MONTH FROM week_date::DATE) AS month_number,
    EXTRACT(YEAR FROM week_date::DATE) AS calendar_year
FROM 
    data_mart.weekly_sales;
```
![week,month,year](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/2977b560-ff5f-4c2f-90b5-5462399b803a)

**Adding `age_band` and `demographic` Columns**

```sql
-- Add a new column called `age_band` based on segment values
-- Add a new `demographic` column based on the first letter of segment values
SELECT 
    region,
    CASE WHEN segment = 'null' THEN 'unknown'
        ELSE segment
    END AS segment,
    COALESCE(
        CASE WHEN segment LIKE '%1' THEN 'Young Adults'
             WHEN segment LIKE '%2' THEN 'Middle Aged'
             WHEN segment LIKE '%3' OR segment LIKE '%4' THEN 'Retirees'
             ELSE NULL
        END, 'unknown') AS age_band,
    COALESCE(
        CASE WHEN segment LIKE 'F%' THEN 'Families'
             WHEN segment LIKE 'C%' THEN 'Couples'
             ELSE NULL
        END, 'unknown') AS demographic
FROM data_mart.weekly_sales;
```
![agebanddemographic](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/d595809a-c875-49d5-9fe0-af77aaa61c02)


-- Ensure all `null` string values with an `unknown` string value in the original segment column as well as the new `age_band` and `demographic` columns
```sql
-- Replacing `null` Values with `unknown`
All `null` values were changed to `unknown` in the `segment`, `age_band`, and `demographic` columns within the SQL query using `CASE` statements.
```
**Generate a new `avg_transaction` column as the `sales` value divided by `transactions` rounded to 2 decimal places for each record**

```sql
-- Generate a new `avg_transaction` column as the result of dividing `sales` by `transactions` rounded to 2 decimal places
SELECT 
    ROUND((sales / transactions), 2) AS avg_transactions
FROM 
    data_mart.weekly_sales;
```
![avg_trans](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/8d8c6e41-fd6d-4a0b-8804-e67b5f7cf6d3)


## Creating the `clean_weekly_sales` Table

```sql
-- Create a new table named `clean_weekly_sales` and populate it with cleaned data
DROP TABLE IF EXISTS clean_weekly_sales;
CREATE TABLE clean_weekly_sales AS
SELECT 
    week_date::DATE,
    EXTRACT(WEEK FROM week_date::DATE) AS week_number,
    EXTRACT(MONTH FROM week_date::DATE) AS month_number,
    EXTRACT(YEAR FROM week_date::DATE) AS calendar_year,
    region,
    platform,
    CASE WHEN segment = 'null' THEN 'unknown'
         ELSE segment
    END AS segment,
    COALESCE(
        CASE WHEN segment LIKE '%1' THEN 'Young Adults'
             WHEN segment LIKE '%2' THEN 'Middle Aged'
             WHEN segment LIKE '%3' OR segment LIKE '%4' THEN 'Retirees'
             ELSE NULL
        END, 'unknown') AS age_band,
    COALESCE(
        CASE WHEN segment LIKE 'F%' THEN 'Families'
             WHEN segment LIKE 'C%' THEN 'Couples'
             ELSE NULL
        END, 'unknown') AS demographic,
    customer_type,
    transactions,
    sales,
    ROUND((sales / transactions), 2) AS avg_transactions
FROM data_mart.weekly_sales;
```
## Checking the `clean_weekly_sales` Table

```sql
-- View the contents of the newly created `clean_weekly_sales` table
SELECT 
    *
FROM 
    clean_weekly_sales;
```
Output:
![output](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/0ffe05a8-3634-48d0-a511-f326ed2bf785)
![output2](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/84deeb45-5317-42ce-8921-c378416cb638)


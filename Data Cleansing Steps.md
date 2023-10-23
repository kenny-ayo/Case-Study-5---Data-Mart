## 1.	Data Cleaning steps
In a single query, perform the following operations and generate a new table in the data_mart schema named clean_weekly_sales:

•	Convert the week_date to a DATE format

•	Add a week_number as the second column for each week_date value, for example any value from the 1st of January to 7th of January will be 1, 8th to 14th will be 2 etc

•	Add a month_number with the calendar month for each week_date value as the 3rd column

•	Add a calendar_year column as the 4th column containing either 2018, 2019 or 2020 values

•	Add a new column called age_band after the original segment column using the following mapping on the number inside the segment value

![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/36f8f539-2da9-4f1f-96e9-87ed401fad99)


•	Add a new demographic column using the following mapping for the first letter in the segment values

![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/fbb0a459-e20b-4831-a260-6f3636536a2d)


•	Ensure all null string values with an "unknown" string value in the original segment column as well as the new age_band and demographic columns
•	Generate a new avg_transaction column as the sales value divided by transactions rounded to 2 decimal places for each record

## Task 1: convert the week_date to a DATE format
-- First convert select the whole data to check how the date is formatted
***

```sql

SELECT *
FROM data_mart.weekly_sales;

```

![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/9809f12e-5177-4782-b9a7-0ba3f171d039)

-- Then we convert week_date column to date using this formula;
***
```sql
SELECT
      week_date :: date
FROM data_mart.weekly_sales;
```

![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/64396e09-e425-4b99-96d3-06fb8e74f44e)

-- Add week_number as second column, month_number  as third column and calendar_year as fourth column
***
```sql
SELECT 
       week_date :: DATE,
       EXTRACT(WEEK FROM week_date :: DATE) AS week_number,
       EXTRACT(MONTH FROM week_date :: DATE) AS month_number,
       EXTRACT(YEAR FROM week_date :: DATE) AS calendar_year
FROM 
     data_mart.weekly_sales;
```

![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/ee0b433f-fdc5-429e-b831-d032a6aa1f32)

--Add a new column called age_band after the original segment column using the following mapping on the number inside the segment value

--Add a new demographic column using the following mapping for the first letter in the segment values
***
```sql
SELECT 
       region,
       CASE WHEN segment = 'null' THEN 'unknown'
	                                ELSE segment
                                   END AS segment,
       COALESCE(CASE WHEN segment LIKE '%1' THEN 'Young Adults'
	                   WHEN segment LIKE '%2' THEN 'Middle Aged'
		                 WHEN segment LIKE '%3'
	                     OR segment LIKE '%4' THEN 'Retirees'
                                            ELSE NULL
                                            END, 'unknown') AS age_band,
        COALESCE(CASE WHEN segment LIKE 'F%' THEN 'Families'
                      WHEN segment LIKE 'C%' THEN 'Couples'
                                             ELSE NULL
                                             END, 'unknown') AS demographic
FROM data_mart.weekly_sales;
```

![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/93462862-3fd2-443d-98bf-bc6a9ea734c6)

-- Ensure all null string values with an "unknown" string value in the original segment column as well as the new age_band and demographic columns
**All NULL** was changed to **unknown** in the case statements

Generate a new avg_transaction column as the sales value divided by transactions rounded to 2 decimal places for each record
***
```sql
SELECT 
      ROUND((sales / transactions), 2) AS avg_transactions
FROM 
      data_mart.weekly_sales;
```

![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/378cf6af-f697-4321-9a26-e8c5db35cdc1)

--As stated, create a new table named clean_weekly_sales
***
```sql
DROP TABLE IF EXISTS clean_weekly_sales;
CREATE TABLE clean_weekly_sales
AS
SELECT 
       week_date :: DATE,
	     EXTRACT(WEEK FROM week_date :: DATE) AS week_number,
       EXTRACT(MONTH FROM week_date :: DATE) AS month_number,
	     EXTRACT(YEAR FROM week_date :: DATE) AS calendar_year,
	     region,
	     platform,
	     CASE WHEN segment = 'null' THEN 'unknown'
	                                ELSE segment 
			                             END AS segment,
	     COALESCE(CASE WHEN segment LIKE '%1' THEN 'Young Adults'
	                   WHEN segment LIKE '%2' THEN 'Middle Aged'
			               WHEN segment LIKE '%3'
			                 OR segment LIKE '%4' THEN 'Retirees'
			                                      ELSE NULL
			                                       END, 'unknown') AS age_band,
	     COALESCE(CASE WHEN segment LIKE 'F%' THEN 'Families'
		                 WHEN segment LIKE 'C%' THEN 'Couples'
			                                      ELSE NULL
			                                       END, 'unknown') AS demographic,
	     customer_type,
	     transactions,
	     sales,
       ROUND((sales / transactions), 2) AS avg_transactions
FROM data_mart.weekly_sales;
```
--Check the newly table created
***
```sql
SELECT 
       *
FROM 
    clean_weekly_sales;
```
Output:
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/a8fe32d8-3560-4ea9-9dad-98eb3fa7805b) ![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/09bf30fe-738c-458e-af46-7699f6df3b28)


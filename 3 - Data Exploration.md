## Case Study #5 - Data Exploration

## Questions to be explored

- What day of the week is used for each week_date value?
- What range of week numbers are missing from the dataset?
- How many total transactions were there for each year in the dataset?
- What is the total sales for each region for each month?
- What is the total count of transactions for each platform
- What is the percentage of sales for Retail vs Shopify for each month?
- What is the percentage of sales by demographic for each year in the dataset?
- Which age_band and demographic values contribute the most to Retail sales?
- Can we use the avg_transaction column to find the average transaction size for each year for Retail vs Shopify? If not - how would you calculate it instead?

**1. What `day of the week` is used for each `week_date` value?**

```sql
-- Determining the Day of the Week for Each `week_date`
SELECT
    DISTINCT to_char(week_date, 'Day') AS day_of_week
FROM
    clean_weekly_sales;
```
![Q1](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/9ff86e99-b812-400e-be34-f15482378f17)


**2. What range of `week numbers` are missing from the dataset?**

```sql
-- Identifying Missing Week Numbers in the Dataset
SELECT
    sub.week_no::INT
FROM (
    SELECT
        generate_series(1, 52) AS week_no
    ) sub
LEFT JOIN
    clean_weekly_sales AS cws
ON
    sub.week_no = CAST(cws.week_number AS INT)
WHERE
    cws.week_number IS NULL;

-- This query produced 27 outputs, the missing week include, week 1 - week 12 and week 37 - week 52
```
![Q2](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/d5fcc838-f26f-4d5f-b71d-6034f5fc72a2)
  

**3. How many total `transactions` were there for each `year` in the dataset?**

```sql
-- Calculate the total number of transactions for each calendar year in the dataset
SELECT 
    calendar_year,
    SUM(transactions) AS total_transactions
FROM 
    clean_weekly_sales
GROUP BY 
    calendar_year
ORDER BY 
    calendar_year;
```
![q3](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/b0e7447c-b37d-4855-91ad-0a01d6d787af)


**4. What is the `total sales` for each `region` for each `month`?**

```sql
-- Calculate the total sales for each region for each month in the dataset
SELECT
    month_number,
    to_char(week_date, 'Month') AS month,
    region,
    SUM(sales) AS total_sales
FROM clean_weekly_sales
GROUP BY
    month_number,
    to_char(week_date, 'Month') AS month,
    region
ORDER BY
    month_number,
    region;
```
![q4](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/5cfd30f9-31a0-46a9-a8f9-54cc229f3f0f)


**5. What is the `total count of transactions` for each `platform`***

```sql
-- Determine the total count of transactions for each platform
SELECT
    platform,
    COUNT(transactions) AS total_count_of_trans
FROM
    clean_weekly_sales
GROUP BY
    platform
ORDER BY
    total_count_of_trans DESC;
```
![q5](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/55735df0-3101-4b33-8eb7-cea59d1fd1d9)

**6. What is the percentage of `sales` for Retail vs Shopify for each `month`?**
```sql
-- Calculate the percentage of sales for Retail and Shopify for each month
WITH monthly_sales AS (
    SELECT 
        calendar_year,
        month_number,
        platform,
        SUM(sales) AS total_sales
    FROM clean_weekly_sales
    GROUP BY 
        calendar_year,
        month_number,
        platform
)

SELECT 
    calendar_year,
    month_number,
    CONCAT(ROUND(100 * MAX(CASE WHEN platform = 'Retail' THEN total_sales ELSE NULL END) / SUM(total_sales), 2), '%') AS retail_pct,
    CONCAT(ROUND(100 * MAX(CASE WHEN platform = 'Shopify' THEN total_sales ELSE NULL END) / SUM(total_sales), 2), '%') AS shopify_pct
FROM monthly_sales
GROUP BY 
    calendar_year,
    month_number;
```
![q6](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/5930ee74-6855-459a-8cf6-6cd660532b1b)


**7. What is the percentage of `sales` by `demographic` for each `year` in the dataset?***

```sql
-- Calculate the percentage of sales by demographic for each year in the dataset
WITH monthly_sales AS (
    SELECT 
        calendar_year,
        demographic,
        SUM(sales) AS total_sales
    FROM clean_weekly_sales
    GROUP BY 
        calendar_year,
        demographic
)

SELECT 
    calendar_year,
    CONCAT(ROUND(100 * MAX(CASE WHEN demographic = 'Families' THEN total_sales ELSE NULL END) / SUM(total_sales), 2), '%') AS families_pct,
    CONCAT(ROUND(100 * MAX(CASE WHEN demographic = 'Couples' THEN total_sales ELSE NULL END) / SUM(total_sales), 2), '%') AS couples_pct
FROM 
    monthly_sales
GROUP BY 
    calendar_year;
```
![q7](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/5a0b01e2-1216-468f-92bd-69165bfa9bb9)


**8. Which `age_band` and `demographic` values contribute the most to Retail `sales`?***

```sql
-- Determine the `age_band` and `demographic` values that contribute the most to Retail sales
SELECT 
    platform,
    age_band,
    demographic,
    SUM(sales) AS retail_sales,
    ROUND(100 * SUM(sales) / SUM(SUM(sales)) OVER (), 1) AS contribution_pct
FROM 
    clean_weekly_sales
WHERE 
    platform = 'Retail'
GROUP BY 
    platform,
    age_band,
    demographic
ORDER BY 
    retail_sales DESC;
```
![q8a](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/100f0532-6ac5-4126-b199-a556c002d1f4)

**Alternative Approach: Identifying Top Contributors to `Retail Sales`**

```sql
-- Another approach to identify top contributors to Retail sales by `age_band` and `demographic` values
WITH Retail_Summary AS (
    SELECT
        platform,
        age_band,
        demographic,
        SUM(sales) AS retail_sales
    FROM
        clean_weekly_sales
    WHERE
        platform = 'Retail'
    GROUP BY
        platform,
        age_band,
        demographic
)

SELECT
    age_band,
    demographic,
    retail_sales,
    RANK() OVER (ORDER BY retail_sales DESC) AS contribution_rank
FROM
    Retail_Summary
WHERE
    platform = 'Retail'
ORDER BY
    retail_sales DESC;
--  It uses a common table expression (CTE) and the RANK() function to determine the contribution rank, providing a different perspective on the same analysis.
```
![q8b](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/01e462ce-0c67-47a9-bdb2-a84ef7373a84)

**9. Can we use the `avg_transaction` column to find the average transaction size for each `year` for Retail vs Shopify? If not - how would you calculate it instead?**

```sql
-- Determining the correct calculation average transaction size for each year and platform
SELECT
    Calendar_year,
    platform,
    ROUND(SUM(sales) / SUM(transactions), 2) AS correct_avg_trans_size,
    ROUND(AVG(avg_transactions), 2) AS incorrect_avg_trans_size
FROM
    clean_weekly_sales
GROUP BY
    Calendar_year,
    platform
ORDER BY
    Calendar_year,
    platform;
```
![q10](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/4f880aad-edb0-47e6-9263-7c9bbd121c1d)

**The `correct_avg_trans_size` is a more accurate measure because it divides the `total sales` by the `total transactions`. In contrast, the `incorrect_avg_trans_size` uses the `avg_transactions` column, resulting in an average of averages, which may not accurately represent the true average transaction size.**

## Case Study #5 - Before & After Analysis

This technique is usually used when we inspect an important event and want to inspect the impact before and after a certain point in time.

Taking the week_date value of 2020-06-15 as the baseline week where the Data Mart sustainable packaging changes came into effect.

We would include all week_date values for 2020-06-15 as the start of the period after the change and the previous week_date values would be before

Using this analysis approach - answer the following questions:

- What is the total sales for the 4 weeks before and after 2020-06-15? What is the growth or reduction rate in actual values and percentage of sales?
- What about the entire 12 weeks before and after?
- How do the sale metrics for these 2 periods before and after compare with the previous years in 2018 and 2019?

## 1. What is the total sales for the 4 weeks before and after 2020-06-15? What is the growth or reduction rate in actual values and percentage of sales?

```sql
-- Determining the week_number for 2020-06-15
SELECT
    DISTINCT week_number
FROM
    clean_weekly_sales
WHERE
    week_date = '2020-06-15';
```
![a1](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/d5f02f59-0510-425e-be63-8223919d7d05)


**This query identifies the `week_number` associated with the date `2020-06-15` in the `clean_weekly_sales` dataset, which is used as a reference point for calculating the `total sales` for the 4 weeks before and after this `date`.**

**Analyzing Sales Impact Before and After Packaging Changes**
```sql
-- Calculate the sales difference and percentage of sales 4 weeks before and after packaging changes around `2020-06-15`.
WITH before_after_packaging AS (
    SELECT
        week_number,
        SUM(sales) AS total_sales
    FROM 
        clean_weekly_sales
    WHERE 
        week_number BETWEEN 21 AND 28
        AND calendar_year = 2020
    GROUP BY 
        week_number
)

SELECT 
    after_packaging - before_packaging AS sales_difference,
    ROUND(100 * (after_packaging - before_packaging) / before_packaging, 2) AS percentage_of_sales
FROM (
    SELECT 
        SUM(CASE WHEN week_number BETWEEN 21 AND 24 THEN total_sales END) AS before_packaging,
        SUM(CASE WHEN week_number BETWEEN 25 AND 28 THEN total_sales END) AS after_packaging
    FROM before_after_packaging
) sub;
```
![a2](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/35e0ab64-a16c-4442-b30a-7587ab4489bc)


## 2. What about the entire 12 weeks before and after?

**Assessing Sales Impact Over 12 Weeks Before and After Packaging Changes**
```sql
-- Evaluate the sales difference and percentage of sales over the 12 weeks before and after a specific date.
WITH before_after_packaging AS (
    SELECT
        week_number,
        SUM(sales) AS total_sales
    FROM 
        clean_weekly_sales
    WHERE 
        week_number BETWEEN 13 AND 37
        AND calendar_year = 2020
    GROUP BY 
        week_number
    ORDER BY 
        week_number
)

SELECT 
    after_packaging - before_packaging AS sales_difference,
    ROUND(100 * (after_packaging - before_packaging) / before_packaging, 2) AS percentage_of_sales
FROM (
    SELECT 
        SUM(CASE WHEN week_number BETWEEN 13 AND 24 THEN total_sales END) AS before_packaging,
        SUM(CASE WHEN week_number BETWEEN 25 AND 37 THEN total_sales END) AS after_packaging
    FROM before_after_packaging
) sub;
```
![a3](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/70d16e04-9f96-491c-9ba4-9443e93e7f4a)

## 3. How do the sale metrics for these 2 periods before and after compare with the previous years in 2018 and 2019?

**Compare sales metrics for the 4 weeks before and after packaging changes with the previous years (2018 and 2019).**
```sql
WITH before_after_packaging AS (
    SELECT
        calendar_year,
        week_number,
        SUM(sales) AS total_sales
    FROM 
        clean_weekly_sales
    WHERE 
        week_number BETWEEN 21 AND 28
        AND calendar_year IN (2018, 2019)
    GROUP BY
        calendar_year,
        week_number
    ORDER BY 
        calendar_year,
        week_number
)

SELECT 
    calendar_year,
    after_packaging - before_packaging AS sales_difference,
    ROUND(100 * (after_packaging - before_packaging) / before_packaging, 2) AS percentage_of sales
FROM (
    SELECT 
        calendar_year,
        SUM(CASE WHEN week_number BETWEEN 21 AND 24 THEN total_sales END) AS before_packaging,
        SUM(CASE WHEN week_number BETWEEN 25 AND 28 THEN total_sales END) AS after_packaging
    FROM before_after_packaging
    GROUP BY 
        calendar_year
) sub;
```
![a3b](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/536f7d38-2d3e-490c-b36d-37043876caa2)

**Evaluate sales metrics for the 12 weeks before and after the specified periods in `2019` and `2018`.**

```sql
WITH before_after_packaging AS (
    SELECT
        calendar_year,
        week_number,
        SUM(sales) AS total_sales
    FROM 
        clean_weekly_sales
    WHERE 
        week_number BETWEEN 13 AND 37
        AND calendar_year IN (2018, 2019)
    GROUP BY
        calendar_year,
        week_number
    ORDER BY 
        calendar_year,
        week_number
)

SELECT
    calendar_year,
    after_packaging - before_packaging AS sales_difference,
    ROUND(100 * (after_packaging - before_packaging) / before_packaging, 2) AS percentage_of_sales
FROM (
    SELECT
        calendar_year,
        SUM(CASE WHEN week_number BETWEEN 13 AND 24 THEN total_sales END) AS before_packaging,
        SUM(CASE WHEN week_number BETWEEN 25 AND 37 THEN total_sales END) AS after_packaging
    FROM before_after_packaging
    GROUP BY calendar_year
) sub;
```
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/3209df12-bce4-44c2-b615-ce9e7597e990)



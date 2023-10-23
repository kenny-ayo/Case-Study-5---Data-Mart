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

- What day of the week is used for each week_date value?
***
```SQL
SELECT
       DISTINCT to_char(week_date, 'Day') AS day_of_week 
FROM
       clean_weekly_sales;
```
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/ca6c3172-92e2-4507-b9e9-336bdd0054b5)

- What range of week numbers are missing from the dataset? 
***
```SQL
SELECT
      sub.week_no::INT	  
FROM (
       SELECT
            generate_series(1, 52) AS week_no
       ) sub
LEFT JOIN
        clean_weekly_sales as cws
ON
   sub.week_no = CAST(cws.week_number AS INT)
WHERE
   cws.week_number IS null
```
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/a9302f58-068c-42f2-b0b2-0d1188694f4f)  
The data contains 27 output this is just a sample of what it should look like

- How many total transactions were there for each year in the dataset?
***
```SQL
SELECT 
      calendar_year,
      SUM(transactions) as total_transactions
FROM 
      clean_weekly_sales
GROUP BY 
      calendar_year
ORDER BY 
      calendar_year;
```
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/43d459a4-2dda-4765-a325-16968e3fb895)

- What is the total sales for each region for each month? 
***
```SQL
SELECT
      month_number,
      to_char(week_date, 'Month') AS month,
      region,
	    SUM(sales) as total_sales
FROM clean_weekly_sales
GROUP BY
        month_number,
        to_char(week_date, 'Month') AS month,
        region
ORDER BY
        month_number,
        region;
```
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/590542e2-d51d-4d1c-81b1-e6a127ee388b)

- What is the total count of transactions for each platform
***
```SQL
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
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/761d61e0-d748-4d04-a1ea-9c743fd298b9)

- What is the percentage of sales for Retail vs Shopify for each month? 
***
```SQL
WITH monthly_sales AS (SELECT 
                            calendar_year,
                            month_number,
	                          platform,
	                          SUM(sales) AS total_sales
                        FROM clean_weekly_sales
                        GROUP BY calendar_year,
                                 month_number,
	                               platform)

SELECT 
      calendar_year,
      month_number,
      CONCAT(ROUND(100 * MAX(CASE WHEN platform = 'Retail'
					                        THEN total_sales ELSE NULL END) / SUM(total_sales), 2), '%') retail_pct,
	    CONCAT(ROUND(100 * MAX(CASE WHEN platform = 'Shopify' 
		                              THEN total_sales ELSE NULL END) / SUM(total_sales), 2),'%') shopify_pct
FROM monthly_sales
GROUP BY calendar_year,
         month_number;
```
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/4c5ba0c5-1e18-426e-a12c-54c36da93439)

- What is the percentage of sales by demographic for each year in the dataset? 
***
```SQL
WITH monthly_sales AS (SELECT 
                              calendar_year,
	                            demographic,
	                            SUM(sales) AS total_sales
                        FROM clean_weekly_sales
                        GROUP BY calendar_year,
	                               demographic)
                              
SELECT 
       calendar_year,
       CONCAT(ROUND(100 * MAX(CASE WHEN demographic = 'Families'
				                           THEN total_sales ELSE NULL END) / SUM(total_sales), 2), '%') families_pct,
       CONCAT(ROUND(100 * MAX(CASE WHEN demographic = 'Couples' 
				                           THEN total_sales ELSE NULL END) / SUM(total_sales), 2),'%') couples_pct
FROM 
       monthly_sales
GROUP BY 
       calendar_year;
```
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/5b573baa-b7a5-4fce-9d5b-e42322a10577)

- Which age_band and demographic values contribute the most to Retail sales? 
***
```SQL
SELECT 
      platform,
      age_band,
      demographic,
	    SUM(sales) as retail_sales,
	    ROUND(100 * SUM(sales) / SUM(SUM(sales)) OVER (), 1) AS cont_pct
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
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/e324793b-8850-4342-a2db-e3dc72b9fd55)

**Another way to go about this**
***
```SQL
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
```
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/8be0a347-0342-4ebb-bfec-ea13213b282c)

- Can we use the avg_transaction column to find the average transaction size for each year for Retail vs Shopify? If not - how would you calculate it instead?
***
```SQL
SELECT
      Calendar_year,
      platform,
      ROUND(SUM(sales)/SUM(transactions), 2) AS correct_avg_trans_size,
      ROUND(AVG(avg_transactions), 2) AS incorrect_Avg_trans_size
FROM
      clean_weekly_sales
GROUP BY
         Calendar_year,
         platform
ORDER BY
         Calendar_year,
         platform;
```
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/6a353810-d69f-4d88-9fa7-229af14e0948)

 

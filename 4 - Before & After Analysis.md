## Case Study #5 - Before & After Analysis
1 - week_number for this date is 25, 4 weeks before will be 21, while 4 weeks after will be 28
***
```SQL
SELECT
      DISTINCT week_number
FROM
      clean_weekly_sales
WHERE
      week_date = '2020=06-15';
```
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/9101445c-4fc4-44af-ac54-955ab09331b8)
***
```SQL
WITH before_after_packaging  AS (
	                                SELECT
                                     week_number,
                                     SUM(sales) AS total_sales
                                  FROM 
                                     clean_weekly_sales
                                  WHERE 
                                     week_number BETWEEN 21 AND 28
                                                 AND calendar_year = 2020
                                  GROUP BY 
	                                      week_number)
								

SELECT 
        after_packaging - before_packaging AS sales_difference,
        ROUND(100 *(after_packaging - before_packaging) / before_packaging, 2) AS percentage_of_sales
FROM(
      SELECT 
            SUM(CASE WHEN week_number BETWEEN 21 AND 24 
                     THEN total_sales END) AS before_packaging,
            SUM(CASE WHEN week_number BETWEEN 25 AND 28 
                     THEN total_sales END) AS after_packaging
      FROM before_after_packaging) sub;
```
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/4eed13ca-0b75-4335-96fa-fd8ee805e784)


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

- Since 2020-06-15 is the baseline line date the week_number for 12 weeks before will be 13 and after will be 37
***
```SQL
WITH before_after_packaging AS(
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
                                       week_number)
								
SELECT 
       after_packaging - before_packaging AS sales_difference,
       ROUND(100 *(after_packaging - before_packaging) / before_packaging, 2) AS percentage_of_sales
FROM(
     SELECT 
	   SUM(CASE WHEN week_number BETWEEN 13 AND 24 
		    THEN total_sales END) AS before_packaging,
           SUM(CASE WHEN week_number BETWEEN 25 AND 37 
		    THEN total_sales END) AS after_packaging
     FROM before_after_packaging) sub;
```
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/03d84483-ab94-40e2-96f8-08c91b10c5ef)
- 4_weeks before will be week_number 21 - 24, while 4 weeks after will be week_number 25 - 28  
***
```SQL
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
	                                  week_number)
								
SELECT 
     calendar_year,
     after_packaging - before_packaging AS sales_difference,
     ROUND(100 *(after_packaging - before_packaging) / before_packaging, 2) AS percentage_of_sales
FROM(
      SELECT 
           calendar_year,
	   SUM(CASE WHEN week_number BETWEEN 21 AND 24 
	            THEN total_sales END) AS before_packaging,
           SUM(CASE WHEN week_number BETWEEN 25 AND 28 
		    THEN total_sales END) AS after_packaging
       FROM 
            before_after_packaging
       GROUP BY 
            calendar_year) sub;
```
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/e90f2fad-141c-466c-ad32-a336d8a29a1c)

For 12 weeks before and after these 2 periods in 2019 and 2018 
***
```SQL
WITH before_after_packaging AS (
	                         SELECT
	                              calendar_year,
	                              week_number,
                                      SUM(sales) AS total_sales
                                  FROM 
	                               clean_weekly_sales
                                  WHERE 
	                                week_number BETWEEN 13 AND 37
	                          AND
                                        calendar_year IN (2018, 2019)
                                  GROUP BY
	                                 calendar_year,
	                                  week_number
                                  ORDER BY 
                                          calendar_year,
	                                  week_number)
								
SELECT
     calendar_year,
     after_packaging - before_packaging AS sales_difference,
     ROUND(100 *(after_packaging - before_packaging) / before_packaging, 2) AS percentage_of_sales
FROM(
     SELECT
         calendar_year,
	 SUM(CASE WHEN week_number BETWEEN 13 AND 24 
		  THEN total_sales END) AS before_packaging,
         SUM(CASE WHEN week_number BETWEEN 25 AND 37 
	          THEN total_sales END) AS after_packaging
      FROM before_after_packaging
      GROUP BY calendar_year) sub;
```
![image](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/23c0d50c-36eb-4ea4-8ac0-71c1ee5870f4)


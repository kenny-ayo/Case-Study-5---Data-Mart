## Case Study #5 - Data-Mart
Danny Ma's 8 Weeks SQL Challenge

<p align="center">
<img src="https://8weeksqlchallenge.com/images/case-study-designs/5.png" alt="Image" width="450" height="450">

The case study can be viewed  [here](https://8weeksqlchallenge.com/case-study-5/)

## Introduction
Data Mart is Danny’s latest venture and after running international operations for his online supermarket that specialises in fresh produce - Danny is asking for your support to analyse his sales performance.
In June 2020 - large scale supply changes were made at Data Mart. All Data Mart products now use sustainable packaging methods in every single step from the farm all the way to the customer.
Danny needs your help to quantify the impact of this change on the sales performance for Data Mart and it’s separate business areas.

## Business Question
The key business question he wants you to help him answer are the following:
- What was the quantifiable impact of the changes introduced in June 2020?
- Which platform, region, segment and customer types were the most impacted by this change?
- What can we do about future introduction of similar sustainability updates to the business to minimise impact on sales?

## Available Data
A single table: data_mart.weekly_sales
The Entity Relationship Diagram is shown below with the data types

![case-study-5-erd](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/965b707d-ff98-4eb8-9405-d1eaa659117c)


## Column Dictionary
1.	Data Mart has international operations using a multi-region strategy
2.	Data Mart has both, a retail and online platform in the form of a Shopify store front to serve their customers
3.	Customer segment and customer_type data relates to personal age and demographics information that is shared with Data Mart
4.	transactions is the count of unique purchases made through Data Mart and sales is the actual dollar amount of purchases

## Example Rows
10 random rows are shown in the table output below from data_mart.weekly_sales:

![189491192-978f144f-03b6-4dce-b69b-342e212e48e2](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/5a41a4df-b098-49cc-a422-d9b38118fb2d)

The schema used for this project was copied from the page and pasted in my Postgresql

![Datamart schema](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/assets/92790075/4e06b3a0-bf4c-490d-8172-a6cf984e5083)

## Solutions
- [2 - Data Cleansing Steps](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/blob/main/2%20-%20Data%20Cleansing%20Steps.md)
- [3 - Data Exploration](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/blob/main/3%20-%20Data%20Exploration.md)
- [4 - Before & After Analysis](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/blob/main/4%20-%20Before%20%26%20After%20Analysis.md)
- [5 - Bonus Question](https://github.com/kenny-ayo/Case-Study-5---Data-Mart/blob/main/5%20-%20Bonus%20Question.md)

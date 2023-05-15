Link:https://datalemur.com/questions?category=SQL&difficulty=Easy
## Easy Questions and Solutions(PostgreSQL 14)

### Linkedln
**1.Given a table of candidates and their skills, you're tasked with finding the candidates best suited for an open Data Science job. You want to find candidates who are proficient in Python, Tableau, and PostgreSQL.
Write a query to list the candidates who possess all of the required skills for the job. Sort the output by candidate ID in ascending order.
Assumption:
There are no duplicates in the candidates table.**
***Solution:***
```sql
SELECT candidate_id
FROM candidates
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY candidate_id
HAVING COUNT(skill) = 3
ORDER BY candidate_id;
```

### Facebook
**2.Assume you're given the tables below about Facebook Page and Page likes (as in "Like a Facebook Page").
Write a query to return the IDs of the Facebook pages which do not possess any likes. The output should be sorted in ascending order.**
***Solution***
```sql
SELECT pages.page_id from pages left join page_likes
on pages.page_id=page_likes.page_id
where page_likes.page_id is null;
```

### Tesla
**3.Tesla is investigating production bottlenecks and they need your help to extract the relevant data. Write a query that determines which parts with the assembly steps have initiated the assembly process but remain unfinished.
Assumptions:
parts_assembly table contains all parts currently in production, each at varying stages of the assembly process.
An unfinished part is one that lacks a finish_date.
This question is straightforward, so let's approach it with simplicity in both thinking and solution.
Effective April 11th 2023, the problem statement and assumptions were updated to enhance clarity.**
***Solution***
```sql
SELECT DISTINCT(part) FROM parts_assembly
WHERE finish_date IS NULL;
```

### Twitters
**4.Assume you're given a table Twitter tweet data, write a query to obtain a histogram of tweets posted per user in 2022. Output the tweet count per user as the bucket and the number of Twitter users who fall into that bucket.
In other words, group the users by the number of tweets they posted in 2022 and count the number of users in each group.**
***Solution***
```sql
SELECT r tweet_bucket,count(c) users_num from
(SELECT *,dense_rank() OVER(ORDER BY c) r from
(SELECT user_id,count(user_id) c FROM tweets
GROUP BY 1) t)t1
GROUP BY 1
ORDER BY 1;
```

### New York Times
**5.Write a query that calculates the total viewership for laptops and mobile devices where mobile is defined as the sum of tablet and phone viewership. Output the total viewership for laptops as laptop_reviews and the total viewership for mobile devices as mobile_views.
Effective 15 April 2023, the solution has been updated with a more concise and easy-to-understand approach.**
***Solution***
```sql
SELECT
sum(case when device_type='laptop' then 1 else 0 end) as laptop_view,
sum(case when device_type='tablet' or device_type='phone' then 1 else 0 end) as mobile_views
from viewership;
```

### Linkedin
**6.Assume you are given the table below that shows job postings for all companies on the LinkedIn platform. Write a query to get the number of companies that have posted duplicate job listings.
Clarification:
Duplicate job listings refer to two jobs at the same company with the same title and description.**
***Solution***
```sql
SELECT COUNT(company_id) FROM
                      (SELECT company_id,COUNT(company_id) c_c,COUNT(title) t_c,COUNT(description) d_c FROM job_listings
                      GROUP BY company_id
                      HAVING COUNT(company_id)>=2 and COUNT(title)>=2 and COUNT(description)>=2) as t;
```

### Facebook
**7.Given a table of Facebook posts, for each user who posted at least twice in 2021, write a query to find the number of days between each user’s first post of the year and last post of the year in the year 2021. Output the user and number of the days between each user's first and last post.**
***Solution***
```sql
SELECT user_id, 
MAX(post_date::DATE) - MIN(post_date::DATE) AS days_diff
FROM posts
WHERE DATE_PART('year', post_date::DATE) = 2021 
GROUP BY user_id
HAVING COUNT(post_id)>1;
```

### Microsoft
**8.Write a query to identify the top 2 Power Users who sent the highest number of messages on Microsoft Teams in August 2022. Display the IDs of these 2 users along with the total number of messages they sent. Output the results in descending order based on the count of the messages.
Assumption:
No two users have sent the same number of messages in August 2022.**
***Solution***
```sql
SELECT sender_id,count(sent_date) as no_of_mgs FROM messages
WHERE EXTRACT(MONTH FROM sent_date) = '8' AND EXTRACT(YEAR FROM sent_date) = '2022'
GROUP BY sender_id
ORDER BY no_of_mgs DESC
LIMIT 2;
```

### Robinhood
**9.Assume you're given the tables containing completed trade orders and user details in a Robinhood trading system.
Write a query to retrieve the top three cities that have the highest number of completed trade orders listed in descending order. Output the city name and the corresponding number of completed trade orders.**
***Solution***
```sql
SELECT users.city,count(trades.order_id) as total_orders
FROM trades inner join users
on trades.user_id=users.user_id
where trades.status='Completed'
group by users.city
order by total_orders desc
limit 3;
```

### Amazon
**10.Given the reviews table, write a query to retrieve the average star rating for each product, grouped by month. The output should display the month as a numerical value, product ID, and average star rating rounded to two decimal places. Sort the output first by month and then by product ID.**
***Solution***
```sql
SELECT EXTRACT(MONTH FROM submit_date) AS mth,product_id,ROUND(AVG(stars),2) as avg_stars
FROM reviews
GROUP BY EXTRACT(MONTH FROM submit_date),product_id
ORDER BY mth,product_id;
```

### Facebook
**11.Assume you have an events table on Facebook app analytics. Write a query to calculate the click-through rate (CTR) for the app in 2022 and round the results to 2 decimal places.
Definition and note:
Percentage of click-through rate (CTR) = 100.0 * Number of clicks / Number of impressions
To avoid integer division, multiply the CTR by 100.0, not 100.**
***Solution***
```sql
SELECT app_id,
ROUND(100.0 * SUM(CASE WHEN event_type = 'click' THEN 1 ELSE 0 END) /SUM(CASE WHEN event_type = 'impression' THEN 1 ELSE 0 END), 2)  AS ctr_rate
FROM events
WHERE timestamp >= '2022-01-01' AND timestamp < '2023-01-01'
GROUP BY app_id;
```

### TikTok
**12.Assume you're given tables with information about TikTok user sign-ups and confirmations through email and text. New users on TikTok sign up using their email addresses, and upon sign-up, each user receives a text message confirmation to activate their account.
Write a query to display the user IDs of those who did not confirm their sign-up on the first day, but confirmed on the second day.
Definition:action_date refers to the date when users activated their accounts and confirmed their sign-up through text messages.**
***Solution***
```sql
SELECT user_id
FROM texts t
join emails e
on t.email_id=e.email_id
where action_date::timestamp::date-signup_date::timestamp::date=1;
```

### JPMorgan Chase
**13.Your team at JPMorgan Chase is soon launching a new credit card, and to gain some context, you are analyzing how many credit cards were issued each month.
Write a query that outputs the name of each credit card and the difference in issued amount between the month with the most cards issued, and the least cards issued. Order the results according to the biggest difference.**
***Solution***
```sql
SELECT 
  card_name, 
  MAX(issued_amount) - MIN(issued_amount) AS difference
FROM monthly_cards_issued
GROUP BY card_name
ORDER BY difference DESC;
```

### Alibaba
**14.You're trying to find the mean number of items per order on Alibaba, rounded to 1 decimal place using tables which includes information on the count of items in each order (item_count table) and the corresponding number of orders for each item count (order_occurrences table).**
***Solution***
```sql
SELECT round(SUM((item_count::DECIMAL*order_occurrences::DECIMAL))/SUM(order_occurrences::Decimal),1) FROM items_per_order
```

### CSV Health
**15.CVS Health is trying to better understand its pharmacy sales, and how well different products are selling. Each drug can only be produced by one manufacturer.
Write a query to find the top 3 most profitable drugs sold, and how much profit they made. Assume that there are no ties in the profits. Display the result from the highest to the lowest total profit.
Definition:cogs stands for Cost of Goods Sold which is the direct cost associated with producing the drug.
Total Profit = Total Sales - Cost of Goods Sold**
***Solution***
```sql
SELECT drug,total_sales-cogs FROM pharmacy_sales
ORDER BY 2 DESC
limit 3;
```

### CSV Health
**16.CVS Health is analyzing its pharmacy sales data, and how well different products are selling in the market. Each drug is exclusively manufactured by a single manufacturer.
Write a query to identify the manufacturers associated with the drugs that resulted in losses for CVS Health and calculate the total amount of losses incurred.
Output the manufacturer's name, the number of drugs associated with losses, and the total losses in absolute value. Display the results sorted in descending order with the highest losses displayed at the top.**
***Solution***
```sql
SELECT manufacturer,COUNT(manufacturer),SUM(abs(total_sales-cogs)) total_loss FROM pharmacy_sales
where total_sales<cogs
GROUP BY 1
ORDER BY 3 DESC;
```

### CSV Health
**17.CVS Health is trying to better understand its pharmacy sales, and how well different products are selling.
Write a query to find the total drug sales for each manufacturer. Round your answer to the closest million, and report your results in descending order of total sales.
Because this data is being directly fed into a dashboard which is being seen by business stakeholders, format your result like this: "$36 million".**
***Solution***
```sql
SELECT 
  manufacturer, 
  CONCAT( '$', ROUND(SUM(total_sales) / 1000000), ' million') AS sales_mil 
FROM pharmacy_sales 
GROUP BY manufacturer 
ORDER BY SUM(total_sales) DESC;
```

### UnitedHealth
**18.UnitedHealth has a program called Advocate4Me, which allows members to call an advocate and receive support for their health care needs – whether that's behavioural, clinical, well-being, health care financing, benefits, claims or pharmacy help.
Write a query to find how many UHG members made 3 or more calls. case_id column uniquely identifies each call made.**
***Solution***
```sql
SELECT COUNT(policy_holder_id) member_count FROM
(SELECT policy_holder_id,COUNT(policy_holder_id) FROM callers
GROUP BY 1
HAVING COUNT(policy_holder_id)>=3) t;
```

### 
**19.UnitedHealth Group has a program called Advocate4Me, which allows members to call an advocate and receive support for their health care needs – whether that's behavioural, clinical, well-being, health care financing, benefits, claims or pharmacy help.
Calls to the Advocate4Me call centre are categorised, but sometimes they can't fit neatly into a category. These uncategorised calls are labelled “n/a”, or are just empty (when a support agent enters nothing into the category field).
Write a query to find the percentage of calls that cannot be categorised. Round your answer to 1 decimal place.**
***Solution***
```sql
SELECT
  ROUND (100.0 * 
    COUNT (case_id) FILTER (
      WHERE call_category IS NULL OR call_category = 'n/a')
    / COUNT (case_id), 1) AS uncategorised_call_pct
FROM callers;
```

### Hope you understand the solutions please refre the solutions and practise yourself.

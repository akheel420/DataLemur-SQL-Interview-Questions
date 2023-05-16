LINK:https://datalemur.com/questions?category=SQL&difficulty=Medium

# MEDIUM QUESTIONS AND SOLUTIONS(PostgreSQL 14)

### UBER
**1.Assume you are given the table below on Uber transactions made by users. Write a query to obtain the third transaction of every user. Output the user id, spend and transaction date.**
**Solution**
```sql
SELECT user_id,spend,transaction_date FROM
(SELECT *,row_number() OVER(PARTITION BY user_id ORDER BY transaction_date) r
FROM transactions) t
WHERE r=3;
```

### SNAPCHAT
**2.Assume you're given tables with information on Snapchat users, including their ages and time spent sending and opening snaps.
Write a query to obtain a breakdown of the time spent sending vs. opening snaps as a percentage of total time spent on these activities grouped by age group. Round the percentage to 2 decimal places in the output.
Notes:
Calculate the following percentages:
time spent sending / (Time spent sending + Time spent opening)
Time spent opening / (Time spent sending + Time spent opening)
To avoid integer division in percentages, multiply by 100.0 and not 100.
Effective April 15th, 2023, the solution has been updated and optimised.**
**Solution**
```sql
SELECT 
  age.age_bucket, 
  ROUND(100.0 * 
    SUM(activities.time_spent) FILTER (WHERE activities.activity_type = 'send')/
      SUM(activities.time_spent),2) AS send_perc, 
  ROUND(100.0 * 
    SUM(activities.time_spent) FILTER (WHERE activities.activity_type = 'open')/
      SUM(activities.time_spent),2) AS open_perc
FROM activities
INNER JOIN age_breakdown AS age 
  ON activities.user_id = age.user_id 
WHERE activities.activity_type IN ('send', 'open') 
GROUP BY age.age_bucket;
```

### Twitter
**3.Given a table of tweet data over a specified time period, calculate the 3-day rolling average of tweets for each user. Output the user ID, tweet date, and rolling averages rounded to 2 decimal places.
Notes:
A rolling average, also known as a moving average or running mean is a time-series technique that examines trends in data over a specified period of time.
In this case, we want to determine how the tweet count for each user changes over a 3-day period.
Effective April 7th, 2023, the problem statement, solution and hints for this question have been revised.**
**Solution**
```sql
SELECT    
  user_id,    
  tweet_date,   
  ROUND(AVG(tweet_count) OVER (
    PARTITION BY user_id     
    ORDER BY tweet_date     
    ROWS BETWEEN 2 PRECEDING AND CURRENT ROW)
  ,2) AS rolling_avg_3d
FROM tweets;
```

### AMAZON
**4.Assume you're given a table with information on Amazon customers and their spending on products in different categories, write a query to identify the top two highest-grossing products within each category in the year 2022. The output should include the category, product, and total spend.**
**Solution**
```sql
SELECT category,product,spent FROM
(SELECT *,dense_rank() OVER(PARTITION BY category ORDER BY spent DESC) r FROM 
(SELECT category,product,sum(spend) as spent FROM product_spend
WHERE transaction_date >= '2022-01-01' AND transaction_date <= '2022-12-31' 
GROUP BY category,product
ORDER BY 3 DESC) t) t1
WHERE r<3
```

### TIKTOK
**5.New TikTok users sign up with their emails. They confirmed their signup by replying to the text confirmation to activate their accounts. Users may receive multiple text messages for account confirmation until they have confirmed their new account.
A senior analyst is interested to know the activation rate of specified users in the emails table. Write a query to find the activation rate. Round the percentage to 2 decimal places.
Definitions:
emails table contain the information of user signup details.
texts table contains the users' activation information.
Assumptions:
The analyst is interested in the activation rate of specific users in the emails table, which may not include all users that could potentially be found in the texts table.
For example, user 123 in the emails table may not be in the texts table and vice versa.
Effective April 4th 2023, we added an assumption to the question to provide additional clarity.**
**Solution**
```sql
SELECT ROUND(COUNT(texts.email_id)::DECIMAL/COUNT(DISTINCT emails.email_id),2) AS activation_rate
FROM emails
LEFT JOIN texts
on emails.email_id=texts.email_id and signup_action='Confirmed';
```

### MICROSOFT
**6.A Microsoft Azure Supercloud customer is a company which buys at least 1 product from each product category.
Write a query to report the company ID which is a Supercloud customer.
As of 5 Dec 2022, data in the customer_contracts and products tables were updated.**
**Solution**
```sql
SELECT c.customer_id
FROM customer_contracts c
JOIN products p on c.product_id=p.product_id
GROUP BY 1
HAVING COUNT(DISTINCT(p.product_category))>=(SELECT COUNT(DISTINCT(product_category)) FROM products);
```

### Google
**7.Assume you're given a table with measurement values obtained from a Google sensor over multiple days with measurements taken multiple times within each day.
Write a query to calculate the sum of odd-numbered and even-numbered measurements separately for a particular day and display the results in two different columns. Refer to the Example Output below for the desired format.
Definition:
Within a day, measurements taken at 1st, 3rd, and 5th times are considered odd-numbered measurements, and measurements taken at 2nd, 4th, and 6th times are considered even-numbered measurements.
Effective April 15th, 2023, the question and solution for this question have been revised.**
**Solution**
```sql
WITH ranked_measurements AS (
  SELECT 
    CAST(measurement_time AS DATE) AS measurement_day, 
    measurement_value, 
    ROW_NUMBER() OVER (
      PARTITION BY CAST(measurement_time AS DATE) 
      ORDER BY measurement_time) AS measurement_num 
  FROM measurements
) 

SELECT 
  measurement_day, 
  SUM(measurement_value) FILTER (WHERE measurement_num % 2 != 0) AS odd_sum, 
  SUM(measurement_value) FILTER (WHERE measurement_num % 2 = 0) AS even_sum 
FROM ranked_measurements
GROUP BY measurement_day;
```

### WALMART
**8.Assume you are given the table on Walmart user transactions. Based on a user's most recent transaction date, write a query to obtain the users and the number of products bought.
Output the user's most recent transaction date, user ID and the number of products sorted by the transaction date in chronological order.
P.S. As of 10 Nov 2022, the official solution was changed from output of the transaction date, number of users and number of products to the current output.**
**Solution**
```sql
SELECT transaction_date,user_id,count as purchase_count 
FROM(SELECT transaction_date,user_id,COUNT(product_id),
row_number() OVER(PARTITION BY user_id ORDER BY transaction_date DESC) r
FROM user_transactions
GROUP BY 1,2
ORDER BY 2,1) t
WHERE r=1
ORDER BY 1
```

### ALIBABA
**9.Given a table containing the item count for each order and the frequency of orders with that item count, write a query to determine the mode of the number of items purchased per order on Alibaba. If there are several item counts with the same frequency, you should sort them in ascending order.
Effective April 22nd, 2023, the problem statement and solution have been revised for enhanced clarity.**
**Solution**
```sql
SELECT item_count
FROM items_per_order
WHERE order_occurrences = 
  (SELECT MAX(order_occurrences) FROM items_per_order)
ORDER BY item_count;
```

### JPMORGAN CHASE
**10.Your team at JPMorgan Chase is soon launching a new credit card. You are asked to estimate how many cards you'll issue in the first month.
Before you can answer this question, you want to first get some perspective on how well new credit card launches typically do in their first month.
Write a query that outputs the name of the credit card, and how many cards were issued in its launch month. The launch month is the earliest record in the monthly_cards_issued table for a given card. Order the results starting from the biggest issued amount.**
**Solution**
```sql
with x as
(SELECT *,RANK() OVER(PARTITION BY card_name ORDER BY issue_year,issue_month) r 
FROM monthly_cards_issued)

SELECT card_name,issued_amount FROM x
WHERE r=1
ORDER BY 2 DESC
```

### Verizon
**11.A phone call is considered an international call when the person calling is in a different country than the person receiving the call.
What percentage of phone calls are international? Round the result to 1 decimal.
Assumption:
The caller_id in phone_info table refers to both the caller and receiver.**
**Solution**
```sql
with c AS
(select c.caller_id,c.receiver_id,i.country_id c_c,k.country_id r_c from phone_calls c
join phone_info i
on c.caller_id=i.caller_id
JOIN phone_info k
on c.receiver_id=k.caller_id)

SELECT ROUND((ROUND(sum(CASE WHEN c_c<>r_c THEN 1 ELSE 0 END),0)*100/COUNT(*)),1)
from c
```


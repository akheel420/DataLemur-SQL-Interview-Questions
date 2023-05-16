LINK:

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

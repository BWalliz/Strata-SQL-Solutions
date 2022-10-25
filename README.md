# Strata-SQL-Solutions
A repository for solutions to SQL StrataScratch problems.

## HARD

### QUESTION

### MY SOLUTION

## MEDIUM

### QUESTION - Finding User Purchases ; Amazon

Write a query that'll identify returning active users. A returning active user is a user that has made a second purchase within 7 days of any other of their purchases. Output a list of user_ids of these returning active users.

Table: amazon_transactions
```
id:int64
user_id:int64
item:object
created_at:datetime64[ns]
revenue:int64
```

### MY SOULTION

```SQL
select distinct user_id
from (select
    user_id,
    created_at,
    lag(created_at, 1) over(partition by user_id order by created_at) as previous
    from amazon_transactions
    ) as a
where datediff(day, previous, created_at) <= 7
order by user_id ;
```

### QUESTION

### MY SOLUTION

```SQL
```

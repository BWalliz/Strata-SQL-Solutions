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

## EASY

### QUESTION - Bikes Last Used ; Uber
Find the last time each bike was in use. Output both the bike number and the date-timestamp of the bike's last use (i.e., the date-time the bike was returned). Order the results by bikes that were most recently used.

Table: dc_bikeshare_q1_2012
```
duration:object
duration_seconds:int64
start_time:datetime64[ns]
start_station:object
start_terminal:int64
end_time:datetime64[ns]
end_station:object
end_terminal:int64
bike_number:object
rider_type:object
id:int64
```

### MY SOLUTION
```SQL
select
    bike_number,
    max(end_time) as last_used
from dc_bikeshare_q1_2012
group by bike_number
order by last_used desc ;
```

### QUESTION

### MY SOLUTION
```SQL
```
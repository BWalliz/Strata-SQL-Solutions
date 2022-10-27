# Strata-SQL-Solutions
A repository for solutions to SQL StrataScratch problems.

## HARD

### QUESTION

### MY SOLUTION

## MEDIUM

### QUESTION - Finding User Purchases 
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
### QUESTION - Users by Average Session Time 
Calculate each user's average session time. A session is defined as the time difference between a page_load and page_exit. For simplicity, assume a user has only 1 session per day and if there are multiple of the same events on that day, consider only the latest page_load and earliest page_exit. Output the user_id and their average session time.

Table: facebook_web_log
user_id:int
timestamp:datetime
action:varchar

### MY SOLUTION
```SQL
with cte as (
select
    user_id,
    datediff(second, max(case when action = 'page_load' then timestamp end), min(case when action = 'page_exit' then timestamp end)) as session_time
from facebook_web_log
group by user_id, cast(timestamp as date)
)

select
    user_id,
    convert(float, round(sum(session_time) * 1.0/count(session_time), 1)) as session_time
from cte
where session_time is not null
group by user_id ; 
```

### QUESTION - Ranking Most Active Guests 
Rank guests based on the number of messages they've exchanged with the hosts. Guests with the same number of messages as other guests should have the same rank. Do not skip rankings if the preceding rankings are identical.
Output the rank, guest id, and number of total messages they've sent. Order by the highest number of total messages first.

Table: airbnb_contacts
id_guest:varchar
id_host:varchar
id_listing:varchar
ts_contact_at:datetime
ts_reply_at:datetime
ts_accepted_at:datetime
ts_booking_at:datetime
ds_checkin:varchar
ds_checkout:varchar
n_guests:int
n_messages:int

### MY SOLUTION
```SQL
with cte as (
select
    id_guest,
    sum(n_messages) as sum_n_messages
from airbnb_contacts
group by id_guest    
)

select dense_rank() over(order by sum_n_messages desc) ranking, *
from cte ;
```

## EASY

### QUESTION - Bikes Last Used 
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
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
```
user_id:int
timestamp:datetime
action:varchar
```

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
```
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
```

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

### QUESTION - Income by Title and Gender
Find the average total compensation based on employee titles and gender. Total compensation is calculated by adding both the salary and bonus of each employee. However, not every employee receives a bonus so disregard employees without bonuses in your calculation. Employee can receive more than one bonus.
Output the employee title, gender (i.e., sex), along with the average total compensation.

Tables: sf_employee, sf_bonus
```
id:int
first_name:varchar
last_name:varchar
age:int
sex:varchar
employee_title:varchar
department:varchar
salary:int
target:int
email:varchar
city:varchar
address:varchar
manager_id:int

worker_ref_id:int
bonus:int
```

### MY SOLUTION
```SQL
with cte as (
select
    employee_title,
    sex,
    salary + bonus as 'total_comp'
from sf_employee e
join (select worker_ref_id, sum(bonus) as 'bonus'
from sf_bonus
group by worker_ref_id) as bon
on e.id = bon.worker_ref_id
)
select 
    employee_title,
    sex,
    avg(total_comp) as 'avg_compensation'
from cte
group by employee_title, sex;
```

### QUESTION - Find matching hosts and guests based off gender and nationality
Find matching hosts and guests pairs in a way that they are both of the same gender and nationality.
Output the host id and the guest id of matched pair.

Tables: airbnb_hosts, airbnb_guests
```
host_id:int
nationality:varchar
gender:varchar
age:int

guest_id:int
nationality:varchar
gender:varchar
age:int
```

### MY SOLUTION
```SQL
select distinct
    host_id,
    guest_id
from airbnb_hosts h
join airbnb_guests g
on h.gender = g.gender
and h.nationality = g.nationality ;
```

### QUESTION - Find the top 10 ranked songs in 2010
What were the top 10 ranked songs in 2010?
Output the rank, group name, and song name but do not show the same song twice.
Sort the result based on the year_rank in ascending order.

Table: billboard_top_100_year_end
```
year:int
year_rank:int
group_name:varchar
artist:varchar
song_name:varchar
id:int
```

### MY SOLUTION
```SQL
select distinct top 10
    year_rank as 'rank',
    group_name,
    song_name
from billboard_top_100_year_end
where year = 2010 ;
```

### QUESTION - Number of Units per Nationality
Find the number of apartments per nationality that are owned by people under 30 years old.
Output the nationality along with the number of apartments.
Sort records by the apartments count in descending order.

Tables: airbnb_hosts, airbnb_units
```
host_id:int
nationality:varchar
gender:varchar
age:int

host_id:int
unit_id:varchar
unit_type:varchar
n_beds:int
n_bedrooms:int
country:varchar
city:varchar
```

### MY SOLUTION
```SQL
select distinct
    nationality,
    count(distinct unit_id) as 'apartment_count'
from airbnb_hosts h
join airbnb_units u
on h.host_id = u.host_id
where h.age < 30 and unit_type like 'Apartment'
group by nationality ;
```

### QUESTION - Top Cool Votes
Find the review_text that received the highest number of  'cool' votes.
Output the business name along with the review text with the highest numbef of 'cool' votes.

Table: yelp_reviews
```
business_name:varchar
review_id:varchar
user_id:varchar
stars:varchar
review_date:datetime
review_text:varchar
funny:int
useful:int
cool:int
```

### MY SOLUTION
```SQL
select
    business_name,
    review_text
from yelp_reviews
where cool = (select max(cool) as max_cool_votes from yelp_reviews) ;
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
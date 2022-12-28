# Business-Analytics

## Analyzing Trip Dataset 

- Here, some business insights from the trip dataset are provided via SQL. 
- **Business question: How much time is spent by our customers based on the day of the week**
```
with t_1 as (select  ride_id,started_at, ended_at,
extract(minute from (ended_at-started_at)) as usage_time,
extract(dow from started_at) as day_of_week
from trip)
select day_of_week, sum(usage_time) as time_sum_usage, 
avg(usage_time) as time_avg_usage, count(day_of_week) as num_ride
from t_1
group by day_of_week
order by time_sum_usage desc
```
| Day | time_sum_usage| time_avg_usage| num_ride |
| :---         |     :---:      |          ---: | ---: |
| Saturday   | 2848799     | 17.50306586384861    |  162760|
|Friday     | 2090770       | 15.352086821158986     |  136188|
Sunday      |  1946063      | 17.7740503612235    |   109489
Thursday    | 1746476       |  14.401194000313343  |121273
Monday|1496599|15.783745873717294|94819
Wednesday|1449917|14.485987751146457|100091
Tuesday|1423223|14.554465874460556|97786
- **Business insight: From the above table, mostly, our app is used during the weekend. This suggests that the customers used our bikes for leisure activity during weekends.**  
- **Business question: At what time of the day is our program used the most (top 5)?**:

```
with t_1 as (select  ride_id,started_at,
extract(hour from (started_at)) as start_hour,
extract(dow from started_at) as day_of_week
from trip)

select day_of_week, start_hour, count(*) as num_ride
from t_1
group by day_of_week, start_hour
order by num_usage desc
limit 5 
```
| Day | start_hour| num_ride |
| :---         |     :---:      |      ---: |
| Thursday  | 17     | 13516  
|Friday     | 17      | 13246  
Saturday      |  14      | 12990
Saturday   | 13       |  12954 
Saturday|12|12605
- **Business insight: we can observe that Saturday 12-14 pm and Friday 17pm are among the top 5, which confirms our previous insight i.e. customers use our app for leisure activity in their spare time. Interestingly, Thursday at 17pm is the rank first, which suggests that most likely our customers used the bikes to go home after the job. But, let's dig into our data and find top 5 stations, where our customers started their trip on Thursday at 17pm.**
```
with t_1 as (select  ride_id,started_at, start_station_name,
extract(hour from (started_at)) as start_hour,
extract(dow from started_at) as day_of_week
 from trip)

select start_station_name, count(start_station_name) as num_station
from t_1
where start_hour=17 and day_of_week=4
group by start_station_name
order by num_station desc
limit 5
```
| Station_name | num_ride |
| :---       |     ---: |
|Streeter Dr & Grand Ave |193
| Clinton St & Washington Blvd | 143
| Clark St & Elm St | 117
| Daley Center Plaza | 117
| Millennium Park | 117 
- **Bussiness recommendation: We can send promotions during weekends to encourage customers to use our app because our data suggests they are willing to use our app during weekends. Moreover, we can increase the number of bikes at the above stations to provide better service**.
- **Business question: At which stations our bikes are highly demanded?**
```
select start_station_name, count(start_station_name) as num_trip
from trip
group by start_station_name
order by num_trip desc
limit 5
```
| Station_name | num_trip |
| :---       |     ---: |
|Streeter Dr & Grand Ave |17013
| Michigan Ave & Oak St | 8409
| Millennium Park | 7653
| Wells St & Concord Ln |6939
| Theater on the Lake | 6926
- **Bussiness insight: The above stations generally are our main stations, where we may increase our bikes to improve our service.**
- **Bussiness question: casual customers or member ones, which one is more active?**
```
select member_casual, count(member_casual) as num_member
,avg(extract(minute from (ended_at-started_at))) as avg_trip_duration
from trip
group by member_casual
```
| customer_type | num_customer | average_trip_duration
| :---       |     ---: | ---: |
|causal |442054|18.49|
| member | 380352|12.68|
- **Business insight: As we can observe, the number of our casual customers is more than our members. Also, casual members spent more time with our bikes.**
- **Business recommendation: The result implies that we couldn't convince our members to use our products more than causal customers. Therefore, we should focus on presenting more incentives to our customers.**

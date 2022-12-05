## Creating Table cyclistic_dataset_2021 to merge all 12 datasets
```
CREATE TABLE cyclistic_dataset_2021 (
ride_id nvarchar(255),
rideable_type nvarchar(100),
started_at datetime2,
ended_at datetime2,
start_station_name nvarchar(100),
start_station_id nvarchar(100),
end_station_name nvarchar(100),
end_station_id nvarchar(100),
start_lat float,
start_lng float,
end_lat float,
end_lng float,
member_casual nvarchar(50) )

```
## Combing all the tables into cyclistic_dataset_2021 using union all
```
Insert into dbo.cyclistic_dataset_2021 (ride_id,rideable_type,started_at,ended_at, start_station_name,start_station_id,end_station_name,end_station_id,start_lat,start_lng,end_lat,end_lng,member_casual)
(Select ride_id, rideable_type, started_at, ended_at,start_station_name,start_station_id,end_station_name,end_station_id, start_lat, start_lng, end_lat, end_lng, member_casual
From cyclistic_case_study.dbo.feb_2021)

UNION ALL

(Select ride_id, rideable_type, started_at, ended_at,start_station_name,start_station_id,end_station_name,end_station_id, start_lat, start_lng, end_lat, end_lng, member_casual
From cyclistic_case_study.dbo.March_2021)

UNION ALL

(Select ride_id, rideable_type, started_at, ended_at,start_station_name,start_station_id,end_station_name,end_station_id, start_lat, start_lng, end_lat, end_lng, member_casual
From cyclistic_case_study.dbo.April_2021)

UNION ALL

(Select ride_id, rideable_type, started_at, ended_at,start_station_name,start_station_id,end_station_name,end_station_id, start_lat, start_lng, end_lat, end_lng, member_casual
From cyclistic_case_study.dbo.may_2021)

UNION ALL

(Select ride_id, rideable_type, started_at, ended_at,start_station_name,start_station_id,end_station_name,end_station_id, start_lat, start_lng, end_lat, end_lng, member_casual
From cyclistic_case_study.dbo.june_2021)

UNION ALL

(Select ride_id, rideable_type, started_at, ended_at,start_station_name,start_station_id,end_station_name,end_station_id, start_lat, start_lng, end_lat, end_lng, member_casual
From cyclistic_case_study.dbo.july_2021)

UNION ALL

(Select ride_id, rideable_type, started_at, ended_at,start_station_name,start_station_id,end_station_name,end_station_id, start_lat, start_lng, end_lat, end_lng, member_casual
From cyclistic_case_study.dbo.august_2021)

UNION ALL

(Select ride_id, rideable_type, started_at, ended_at,start_station_name,start_station_id,end_station_name,end_station_id, start_lat, start_lng, end_lat, end_lng, member_casual
From cyclistic_case_study.dbo.sept_2021)

UNION ALL

(Select ride_id, rideable_type, started_at, ended_at,start_station_name,start_station_id,end_station_name,end_station_id, start_lat, start_lng, end_lat, end_lng, member_casual
From cyclistic_case_study.dbo.oct_2021)

UNION ALL

(Select ride_id, rideable_type, started_at, ended_at,start_station_name,start_station_id,end_station_name,end_station_id, start_lat, start_lng, end_lat, end_lng, member_casual
From cyclistic_case_study.dbo.nov_2021)

UNION ALL

(Select ride_id, rideable_type, started_at, ended_at,start_station_name,start_station_id,end_station_name,end_station_id, start_lat, start_lng, end_lat, end_lng, member_casual
From cyclistic_case_study.dbo.dec_2021)

UNION ALL

(Select ride_id, rideable_type, started_at, ended_at,start_station_name,start_station_id,end_station_name,end_station_id, start_lat, start_lng, end_lat, end_lng, member_casual
From cyclistic_case_study.dbo.jan_2022)

```
## Adding a new column ride_length = ended_at - started_at
```

alter table dbo.cyclistic_data_dump
add  ride_length int;

update dbo.cyclistic_data_dump 
set ride_length = datediff(minute, started_at, ended_at)

```
## Adding columns 
  ### 1. day_of_week : Specifies the days in a week ( Sunday, Mon,Tue,..Saturday)
  ### 2. ride_month : Specifies the month name from start_date
  ### 3. ride_year : year of ride
  
```
alter table dbo.cyclistic_data_dump
add day_of_week nvarchar(50),
ride_month nvarchar(50),
ride_year nvarchar(50)

update dbo.cyclistic_data_dump
set day_of_week = DATENAME(weekday,started_at),
ride_month = DATENAME(month,started_at),
ride_year = year(started_at);


```
## add a column with month number from date

```
alter table dbo.cyclistic_data_dump
add month_num int;

update dbo.cyclistic_data_dump
set month_num = DATEPART(month,started_at);


ALTER TABLE dbo.cyclistic_data_dump
ADD ride_start_date date;

update dbo.cyclistic_data_dump
set ride_start_date = cast(started_at as date);
```


## replace docked_bike as casual_bikes

```
update dbo.cyclistic_data_dump
set rideable_type = REPLACE(rideable_type, 'docked_bike', 'classic_bike')
```

## Check for null values, incorrect, duplicate values

```
select count(*) from dbo.cyclistic_data_dump;

--5487318
```
## Delete null values
```
delete from dbo.cyclistic_data_dump
where ride_id is null or
      start_station_name is null or
	  ride_length is null or
	  ride_length = 0 or
	  ride_length <0 or
	  ride_length > 1440;
 ```

--After deleting null values – 4732941


## Types of rides by user types
```
select rideable_type, member_casual, count(*) as num_of_rides
from dbo.cyclistic_data_dump
group by rideable_type, member_casual
order by member_casual, num_of_rides desc;
```

## Calculating the number of rides taken by members in each month…

```
select ride_month, count(*) as total_rides
from dbo.cyclistic_data_dump
where member_casual = 'member'
group by ride_month
order by total_rides desc;
```

## Calculating the number of rides taken by casual in each month…

```
select ride_month, count(*) as total_rides
from dbo.cyclistic_data_dump
where member_casual = 'casual'
group by ride_month
order by total_rides desc;
```

## Calculating the number of rides taken by member in each month…

```
select ride_month, count(*) as total_rides
from dbo.cyclistic_data_dump
where member_casual = 'member'
group by ride_month
order by total_rides desc;
```

## Calculating the number of rides taken by both users in each month…

```
select ride_month, member_casual,count(*) as total_rides
from dbo.cyclistic_data_dump
group by ride_month,member_casual
order by total_rides desc;
```


## Calculate days of week and no of rides by casual

```
select day_of_week,
       count(*) as total_rides
from dbo.cyclistic_data_dump
where member_casual= 'casual' 
group by day_of_week
order by total_rides desc
```

## Calculate days of week and no of rides by both users

```
select day_of_week,member_casual,
       count(*) as total_rides
from dbo.cyclistic_data_dump
where member_casual= 'casual' or member_casual = 'member'
group by day_of_week,member_casual
order by total_rides,member_casual desc
```

## Calculate days of week and no of rides by member

```
select day_of_week,
       count(*) as total_rides
from dbo.cyclistic_data_dump
where member_casual= 'member'
group by day_of_week
order by total_rides desc
```

## Total rides by casual per day

```
select day_of_week,
       count(*) as total_rides
from dbo.cyclistic_data_dump
where member_casual= 'casual'
group by day_of_week
order by total_rides desc
```

## Total rides by both user per day

```
select day_of_week,member_casual,
       count(*) as total_rides
from dbo.cyclistic_data_dump
where member_casual= 'member' or member_casual = 'casual'
group by day_of_week,member_casual
order by total_rides desc, member_casual
```

## Average ride length per day of week by casual

```
select day_of_week,
       avg(ride_length)as average_ride_length
from dbo.cyclistic_data_dump
where member_casual = 'casual'
group by day_of_week
```

## Average ride length per day of week  by member

```
select day_of_week,
       avg(ride_length)as average_ride_length
from dbo.cyclistic_data_dump
where member_casual = 'member'
group by day_of_week;
```

## Average ride length by both per day

```
select day_of_week,member_casual,
       avg(ride_length)as average_ride_length
from dbo.cyclistic_data_dump
group by day_of_week,member_casual;
```

## Top stations by both users

```
SELECT 
        DISTINCT start_station_name, 
        SUM(
            CASE WHEN ride_id = ride_id AND start_station_name = start_station_name THEN 1 ELSE 0 END
            ) AS total,
        SUM(
            CASE WHEN member_casual = 'member' AND start_station_name = start_station_name THEN 1 ELSE 0 END
            ) AS member,
        SUM(
            CASE WHEN member_casual = 'casual' AND start_station_name = start_station_name THEN 1 ELSE 0 END
            ) AS casual
FROM 
        dbo.cyclistic_data_dump
GROUP BY 
        start_station_name
ORDER BY 
        total DESC
```

## Top start station by member

```
SELECT 
        DISTINCT start_station_name,
        SUM(
            CASE WHEN ride_id = ride_id AND start_station_name = start_station_name THEN 1 ELSE 0 END
            ) AS total,
        SUM(
            CASE WHEN member_casual = 'member' AND start_station_name = start_station_name THEN 1 ELSE 0 END
            ) AS member
        
FROM dbo.cyclistic_data_dump
GROUP BY start_station_name
ORDER BY 
        member DESC
 ```       

## Top start station by casual

```
SELECT 
        DISTINCT start_station_name,
        SUM(
            CASE WHEN ride_id = ride_id AND start_station_name = start_station_name THEN 1 ELSE 0 END
            ) AS total,
        SUM(
            CASE WHEN member_casual = 'casual' AND start_station_name = start_station_name THEN 1 ELSE 0 END
            ) AS member
        
FROM dbo.cyclistic_data_dump
GROUP BY start_station_name
ORDER BY 
        member DESC

```



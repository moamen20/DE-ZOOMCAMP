## Question 1
```bash
docker run -it --entrypoint bash python:3.12.8
```

```bash
pip list
```

Ans: <mark>24.3.1</mark>

### Question 2. Understanding Docker networking and docker-compose

Ans: <mark> db:5432 </mark>

## Question 3. Trip Segmentation Count
During the period of October 1st 2019 (inclusive) and November 1st 2019 (exclusive), how many trips, respectively, happened:
Up to 1 mile
In between 1 (exclusive) and 3 miles (inclusive),
In between 3 (exclusive) and 7 miles (inclusive),
In between 7 (exclusive) and 10 miles (inclusive),
Over 10 miles

Ans: <mark>104,793; 198,924; 109,603; 27,678; 35,189</mark> 
```sql
SELECT
    COUNT(CASE WHEN trip_distance <= 1.0 THEN 1 END) AS 0_1_mile,
	COUNT(CASE WHEN trip_distance > 1.0 AND trip_distance <= 3.0 THEN 1 END) AS 1_3_miles,
    COUNT(CASE WHEN trip_distance > 3.0 AND trip_distance <= 7.0 THEN 1 END) AS 3_7_miles,
    COUNT(CASE WHEN trip_distance > 7.0 AND trip_distance <= 10.0 THEN 1 END) AS 7_10_miles,
    COUNT(CASE WHEN trip_distance > 10.0 THEN 1 END) AS over_10_miles
FROM green_taxi_trips
WHERE
    date(lpep_pickup_datetime) >= '2019-10-01' AND
	date(lpep_pickup_datetime) < '2019-11-01' AND
	date(lpep_dropoff_datetime) >= '2019-10-01' AND
    date(lpep_dropoff_datetime) < '2019-11-01';
```
## Question 4. Longest trip for each day
Which was the pick up day with the longest trip distance? Use the pick up time for your calculations.

Ans: <mark>2019-10-31</mark>
```sql
SELECT green_taxi_trips.lpep_pickup_datetime FROM green_taxi_trips
WHERE green_taxi_trips.trip_distance = (select max(green_taxi_trips.trip_distance) from green_taxi_trips)
```
## Question 5. Three biggest pickup zones
Which were the top pickup locations with over 13,000 in total_amount (across all trips) for 2019-10-18?

Consider only lpep_pickup_datetime when filtering by date.

Ans: <mark>East Harlem North, East Harlem South, Morningside Heights</mark>
```sql
SELECT zones."Zone" AS PICKUP_LOCATION, SUM(G.TOTAL_AMOUNT)
FROM GREEN_TAXI_TRIPS G INNER JOIN ZONES ON G."PULocationID" = zones."LocationID"
WHERE DATE(G.lpep_pickup_datetime)='2019-10-18' 
GROUP BY zones."Zone"
HAVING
	SUM(G.TOTAL_AMOUNT) > 13000
```
## Question 6. Largest tip
For the passengers picked up in Ocrober 2019 in the zone name "East Harlem North" which was the drop off zone that had the largest tip?

Ans: <mark>JFK Airport</mark>

```sql
SELECT DROP_ZONE."Zone" as dropoff_zone,MAX(G.TIP_AMOUNT) AS LARGEST_TIP
FROM
	GREEN_TAXI_TRIPS G
	INNER JOIN ZONES PICKUP_ZONE ON G."PULocationID" = PICKUP_ZONE."LocationID"
	INNER JOIN ZONES DROP_ZONE ON G."DOLocationID" = DROP_ZONE."LocationID"
WHERE
	PICKUP_ZONE."Zone" = 'East Harlem North'
	AND G.LPEP_PICKUP_DATETIME >= '2019-10-01'
    AND G.LPEP_PICKUP_DATETIME<'2019-11-01'
GROUP BY DROP_ZONE."Zone"
ORDER BY MAX(G.TIP_AMOUNT) DESC
LIMIT 1
```
## Question 7. Terraform Workflow
Which of the following sequences, respectively, describes the workflow for:

1. Downloading the provider plugins and setting up backend,
2. Generating proposed changes and auto-executing the plan
3. Remove all resources managed by terraform

Ans: <mark> terraform init, terraform apply -auto-approve, terraform destroy </mark>

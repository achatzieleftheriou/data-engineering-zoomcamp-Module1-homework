# data-engineering-zoomcamp-Module1-homework

## Exercise 1
Run docker with the python:3.13 image. Use an entrypoint bash to interact with the container.

What's the version of pip in the image?

The correct answer is: **25.3**

## Exercise 2

Given the following docker-compose.yaml, what is the hostname and port that pgadmin should use to connect to the postgres database?

services:
  db:
    container_name: postgres
    image: postgres:17-alpine
    environment:
      POSTGRES_USER: 'postgres'
      POSTGRES_PASSWORD: 'postgres'
      POSTGRES_DB: 'ny_taxi'
    ports:
      - '5433:5432'
    volumes:
      - vol-pgdata:/var/lib/postgresql/data

  pgadmin:
    container_name: pgadmin
    image: dpage/pgadmin4:latest
    environment:
      PGADMIN_DEFAULT_EMAIL: "pgadmin@pgadmin.com"
      PGADMIN_DEFAULT_PASSWORD: "pgadmin"
    ports:
      - "8080:80"
    volumes:
      - vol-pgadmin_data:/var/lib/pgadmin

volumes:
  vol-pgdata:
    name: vol-pgdata
  vol-pgadmin_data:
    name: vol-pgadmin_data

The correct answer is **db:5432**

## Exercise 3

For the trips in November 2025 (lpep_pickup_datetime between '2025-11-01' and '2025-12-01', exclusive of the upper bound), how many trips had a trip_distance of less than or equal to 1 mile?

```sql
SELECT COUNT(*) AS short_trip_count FROM green_taxi_data 
WHERE lpep_pickup_datetime >= '2025-11-01' AND lpep_pickup_datetime < '2025-12-01' AND trip_distance <= 1;
```
The correct answer is **8007**


## Exercise 4. 

Which was the pick up day with the longest trip distance? Only consider trips with trip_distance less than 100 miles (to exclude data errors).

Use the pick up time for your calculations.

```sql
SELECT DATE(lpep_pickup_datetime) AS pickup_day, MAX(trip_distance) AS longest_trip_distance FROM green_taxi_data 
WHERE trip_distance < 100 GROUP BY pickup_day ORDER BY longest_trip_distance DESC LIMIT 1;
```
The correct answer is **2025-11-14**

## Exercise 5

Which was the pickup zone with the largest total_amount (sum of all trips) on November 18th, 2025?


```sql
SELECT z.Zone AS pickup_zone, SUM(t.total_amount) AS total_revenue FROM green_taxi_data t JOIN taxi_zones z ON t."PULocationID"  = z.LocationID WHERE DATE(t.lpep_pickup_datetime) = '2025-11-18' GROUP BY z.Zone ORDER BY total_revenue DESC LIMIT 1;
```

The correct answer is **East Harlem North**

## Exercise 6 

For the passengers picked up in the zone named "East Harlem North" in November 2025, which was the drop off zone that had the largest tip?

Note: it's tip , not trip. We need the name of the zone, not the ID.


```sql
SELECT dz.Zone AS dropoff_zone, MAX(t.tip_amount) AS largest_tip FROM green_taxi_data t JOIN taxi_zones pz ON t."PULocationID" = pz.LocationID JOIN taxi_zones dz ON t."DOLocationID" = dz.LocationID WHERE pz.Zone = 'East Harlem North' AND t.lpep_pickup_datetime >= '2025-11-01' AND t.lpep_pickup_datetime < '2025-12-01' GROUP BY dz.Zone ORDER BY largest_tip DESC LIMIT 1;
```

The correct answer is **Yorkville West**

## Exercise 7 

Which of the following sequences, respectively, describes the workflow for:

Downloading the provider plugins and setting up backend,
Generating proposed changes and auto-executing the plan
Remove all resources managed by terraform

The answer is **terraform init, terraform apply -auto-approve, terraform destroy**
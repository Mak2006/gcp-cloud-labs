# Create a Streaming Data Pipeline for a Real-Time Dashboard with Cloud Dataflow

### Business Requirment 
In this lab, you own a fleet of New York City taxi cabs and are looking to monitor how well your business is doing in real-time. You will build a streaming data pipeline to capture taxi revenue, passenger count, ride status, and much more and visualize the results in a management dashboard. 
**Algo** 
	-  Set up Bigquery tables, Cloud Storage bucket to be used by Dataflow,  Enable API's if not already
	-  Set up a Cloud Dataflow Pipeline
	-   Connect to a streaming data Topic in Cloud Pub/sub
	-   Ingest streaming data with Cloud Dataflow
	-   Load streaming data into BigQuery
	-   Analyze and visualize the results

**Known Unknowns**
	1. Data schema  - Given - 
	2. Topic creation - Given -  Google maintains a few public Pub/Sub streaming data topics for labs like this one. We'll be using the [NYC Taxi & Limousine Commission’s open dataset](https://data.cityofnewyork.us/) 

**Prepare Bigquery to receive data**
`bq mk taxirides` # create a data set

Create a schema
```
bq mk \
--time_partitioning_field timestamp \
--schema ride_id:string,point_idx:integer,latitude:float,longitude:float,\
timestamp:timestamp,meter_reading:float,meter_increment:float,ride_status:string,\
passenger_count:integer -t taxirides.realtime
```  
**Set up Dataflow pipeline**
Basically connects the pubsub to Bigquery. 
Create job, using template PS to Bq,  topic is `projects/pubsub-public-data/topics/taxirides-realtime`  Bigquery table is `<myprojectid>:taxirides.realtime` Temporary locatin is `gs://<mybucket>/tmp/` Set it run. 
Notes - About template **Cloud Pub/Sub to BigQuery**

This template stages a streaming pipeline that reads JSON-formatted messages  from a Cloud Pub/Sub topic, transforms them using a Javascript user-defined  function (UDF), and writes them to a pre-existing BigQuery table as BigQuery  elements. You can use this template as a quick way to move Cloud Pub/Sub data to  BigQuery.

All the configurations 
![](https://i.imgur.com/5hoLxj4.png)

Job started 
![](https://i.imgur.com/gofbDDu.png)

Job details 
![](https://i.imgur.com/jrBcwRh.png)

**Analyse using Bq**
`SELECT * FROM taxirides.realtime LIMIT 10` sample the data

![](https://i.imgur.com/JZ9N1rP.png)


Play
```

WITH streaming_data AS (

SELECT
  timestamp,
  TIMESTAMP_TRUNC(timestamp, HOUR, 'UTC') AS hour,
  TIMESTAMP_TRUNC(timestamp, MINUTE, 'UTC') AS minute,
  TIMESTAMP_TRUNC(timestamp, SECOND, 'UTC') AS second,
  ride_id,
  latitude,
  longitude,
  meter_reading,
  ride_status,
  passenger_count
FROM
  taxirides.realtime
WHERE ride_status = 'dropoff'
ORDER BY timestamp DESC
LIMIT 100000

)

# calculate aggregations on stream for reporting:
SELECT
 ROW_NUMBER() OVER() AS dashboard_sort,
 minute,
 COUNT(DISTINCT ride_id) AS total_rides,
 SUM(meter_reading) AS total_revenue,
 SUM(passenger_count) AS total_passengers
FROM streaming_data
GROUP BY minute, timestamp
```
 ![Result](https://i.imgur.com/TShTKEK.png)


**Create dashboard**
Open DataStudion

This is found int he Bigquery
![](https://i.imgur.com/R2F4bhh.png)

![Welcome](https://i.imgur.com/x8UqrYn.png)

Authorize 

```
-   Chart type: column chart
-   Date range dimension: dashboard_sort
-   Dimension: dashboard_sort, minute
-   Drill Down: dashboard_sort
-   Metric: SUM() total_rides, SUM() total_passengers, SUM() total_revenue
-   Sort: dashboard_sort Ascending (latest rides first)
```

Explorer view
![](https://i.imgur.com/q8gJHNG.png)

**Create a report**
![](https://i.imgur.com/bKN6z5W.png)

![Stop job](https://i.imgur.com/SNkvslh.png)

[The report PDF](https://drive.google.com/file/d/1KVq05syIpczsU0XXl5Mnim_l_u9jxIvl/view?usp=sharing)



<!--stackedit_data:
eyJoaXN0b3J5IjpbLTgzMjQ2MzE0NywxMDE5ODAzOTYsMTkyNj
k2Nzk2MCwtMTk0ODE4NTc1OCw0NjkyNDE3OTksLTM5NzEyMzEw
NywtNTIzMTE1NTY3LC0xMTE0OTkyODc4LC0xNzg0MjQxMDYyLC
0xNDk2NTE5MzE4LC05OTY5ODM1NTUsLTE1MDcxNzU0MDksMTg1
NTcxMDQ3MSw0NTk2NzYwNTIsMTI3MTMzMjI1M119
-->
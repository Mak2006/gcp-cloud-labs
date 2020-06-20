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


**Analyse using Bq**
`SELECT * FROM taxirides.realtime LIMIT 10` sample the data

Play
```


<!--stackedit_data:
eyJoaXN0b3J5IjpbMjgwNzU1MjgwLC0xNDk2NTE5MzE4LC05OT
Y5ODM1NTUsLTE1MDcxNzU0MDksMTg1NTcxMDQ3MSw0NTk2NzYw
NTIsMTI3MTMzMjI1M119
-->
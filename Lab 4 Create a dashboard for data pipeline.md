# Create a Streaming Data Pipeline for a Real-Time Dashboard with Cloud Dataflow

### Business Requirment 
In this lab, you own a fleet of New York City taxi cabs and are looking to monitor how well your business is doing in real-time. You will build a streaming data pipeline to capture taxi revenue, passenger count, ride status, and much more and visualize the results in a management dashboard. 

In this lab we do the following 
-   Connect to a streaming data Topic in Cloud Pub/sub
-   Ingest streaming data with Cloud Dataflow
-   Load streaming data into BigQuery
-   Analyze and visualize the results

**Known Unknowns**
	1. Data schema
	2. Topic creation - Google maintains a few public Pub/Sub streaming data topics for labs like this one. We'll be using the [NYC Taxi & Limousine Commission’s open dataset](https://data.cityofnewyork.us/) 

**Prereqs** 
	1. Enable Pub Sub API, Dataflow API
	2. SDF
	3. 
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1MDcxNzU0MDksMTg1NTcxMDQ3MSw0NT
k2NzYwNTIsMTI3MTMzMjI1M119
-->
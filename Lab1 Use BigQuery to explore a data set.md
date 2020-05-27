# Use BigQuery to explore a public data set.
This are notes taken as part of the course "## Google Cloud Platform Big Data and Machine Learning Fundamentals" And was practiced with Qwiklabs.

GCP has a lot of public data sets available on which we can run BigQuery. In this lab, we shall  load a data set and query on them. 

Using Google BigQuery we can fire traditional SQL on massive data sets. We can programmaticallyuse this and also a variety of [third-party tools](https://cloud.google.com/bigquery/third-party-tools) that we can use to interact with BigQuery, such as visualizing the data or loading the data. In this lab, we access BigQuery using the web UI.

## Choose and select a data set from existing data sets
GCP->Big Query->Add Data -> choose any data set
### View a data set
View the  schema or preview it 
![Usa Names data set](https://i.imgur.com/ksJrZqC.png)

## Create a query and run it 
```
SELECT
  name, gender,
  SUM(number) AS total
FROM
  `bigquery-public-data.usa_names.usa_1910_2013`
GROUP BY
  name, gender
ORDER BY
  total DESC
LIMIT
  10
```
![Query run results](https://i.imgur.com/nFuFqBW.png)

## Create your own data set and query. 
We now load a custom data set using GCP
![Creating a custome Ds](https://i.imgur.com/pjiC1vv.png)
### Create a table using GCP
### Load data
### Preview table 

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTkwMjQwMjg3NiwtMTY0NjEwNzg1Nyw4NT
E5MjE3MzAsMTkwMDcxMTIzMywtMTA2NDc2MTAzOV19
-->
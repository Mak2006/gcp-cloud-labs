# Lab 2 - Recommend Products using ML with Cloud SQL and Dataproc
These are notes taken as part of the course " Google Cloud Platform Big Data and Machine Learning Fundamentals" And was practiced with Qwiklabs.

Here, we use Cloud SQL to store the data for the recommendation engine built on 	Spark ML. 

Google Cloud SQL is a fully-managed database service that makes it easy to set-up, maintain, manage and administer your relational MySQL and PostgreSQL databases in the cloud.


## Create a Cloud SQL instance
Cloud SQL -> choose type of db (MySQL, Postgre, SQL server). We choose MySQL. We go with basic configuration. Note the password. 

![Creating db](https://i.imgur.com/GOps0w5.png)

![Being provisioned](https://i.imgur.com/dwiqKt1.png)

## Connect to the instance using shell
Open a cloud shell and fire.
`gcloud sql connect rentals --user=root --quiet`
The ip is whitlisted, password supplied and we are connected
![connected to db](https://i.imgur.com/RyVmu6r.png)

`show databases` will show the defualt db's

## Create db and tables
We now create a db using the following script
```
CREATE DATABASE IF NOT EXISTS recommendation_spark;

USE recommendation_spark;

DROP TABLE IF EXISTS Recommendation;
DROP TABLE IF EXISTS Rating;
DROP TABLE IF EXISTS Accommodation;

CREATE TABLE IF NOT EXISTS Accommodation
(
  id varchar(255),
  title varchar(255),
  location varchar(255),
  price int,
  rooms int,
  rating float,
  type varchar(255),
  PRIMARY KEY (ID)
);

CREATE TABLE  IF NOT EXISTS Rating
(
  userId varchar(255),
  accoId varchar(255),
  rating int,
  PRIMARY KEY(accoId, userId),
  FOREIGN KEY (accoId)
    REFERENCES Accommodation(id)
);

CREATE TABLE  IF NOT EXISTS Recommendation
(
  userId varchar(255),
  accoId varchar(255),
  prediction float,
  PRIMARY KEY(userId, accoId),
  FOREIGN KEY (accoId)
    REFERENCES Accommodation(id)
);

SHOW DATABASES;
```
![db created](https://i.imgur.com/fU38fYJ.png)

```
USE recommendation_spark;

SHOW TABLES;
```
we see the 3 tables as intended and they have no data.

## Data sourcing and load in to Cloud SQL
We obtain data from the project, store it in bucket and then load it. 
```
echo "Creating bucket: gs://$DEVSHELL_PROJECT_ID"
gsutil mb gs://$DEVSHELL_PROJECT_ID

echo "Copying data to our storage from public dataset"
gsutil cp gs://cloud-training/bdml/v2.0/data/accommodation.csv gs://$DEVSHELL_PROJECT_ID
gsutil cp gs://cloud-training/bdml/v2.0/data/rating.csv gs://$DEVSHELL_PROJECT_ID

echo "Show the files in our bucket"
gsutil ls gs://$DEVSHELL_PROJECT_ID

echo "View some sample data"
gsutil cat gs://$DEVSHELL_PROJECT_ID/accommodation.csv
```
## View the data
We can view the data using  gsutil
## Loading data into Cloud SQL
Cloud SQL -> Import ->

## Launch Dataproc
Select region
Enable API
Create Cluster
Select master and worker nodes as both n1-standard
Execute the bash script for patching and access
```
echo "Authorizing Cloud Dataproc to connect with Cloud SQL"
CLUSTER=rentals
CLOUDSQL=rentals
ZONE=us-central1-a
NWORKERS=2

machines="$CLUSTER-m"
for w in `seq 0 $(($NWORKERS - 1))`; do
   machines="$machines $CLUSTER-w-$w"
done

echo "Machines to authorize: $machines in $ZONE ... finding their IP addresses"
ips=""
for machine in $machines; do
    IP_ADDRESS=$(gcloud compute instances describe $machine --zone=$ZONE --format='value(networkInterfaces.accessConfigs[].natIP)' | sed "s/\['//g" | sed "s/'\]//g" )/32
    echo "IP address of $machine is $IP_ADDRESS"
    if [ -z  $ips ]; then
       ips=$IP_ADDRESS
    else
       ips="$ips,$IP_ADDRESS"
    fi
done

echo "Authorizing [$ips] to access cloudsql=$CLOUDSQL"
gcloud sql instances patch $CLOUDSQL --authorized-networks $ips
```
Set the instance to connec to 
## Train and apply ML model written in PySpark to create product recommendations
Run the ML model, this is pre created - Assume "Your data science team has created a recommendation model using Apache Spark and written in Python." Obtain this from 
```
gsutil cp gs://cloud-training/bdml/v2.0/model/train_and_apply.py train_and_apply.py
cloudshell edit train_and_apply.py
```
**train_and_apply.py**
```
past it here
```

We have to give access to our data store to train. 
```
# MAKE EDITS HERE
CLOUDSQL_INSTANCE_IP = '<paste-your-cloud-sql-ip-here>'   # <---- CHANGE (database server IP)
CLOUDSQL_DB_NAME = 'recommendation_spark' # <--- leave as-is
CLOUDSQL_USER = 'root'  # <--- leave as-is
CLOUDSQL_PWD  = '<type-your-cloud-sql-password-here>'  # <---- CHANGE
```
## Run ML job on dataproc
Select Job type, cluster and ML job to run. 
Submit the job. Check if it succeeds or fails. 


## Explore inserted rows in Cloud SQL















<!--stackedit_data:
eyJoaXN0b3J5IjpbODI1MTIxMjA1LC0xMzUyOTM4NDAyLDE4MD
A4MzE4NTksLTQ2NjMyNzU3OCw2Mjg5MTY5MywtMTYwNDMzNjY0
MSwxMzA4NDQ4OTc3XX0=
-->
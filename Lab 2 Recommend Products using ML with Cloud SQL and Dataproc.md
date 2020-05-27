# Using Cloud SQL and Spark




























# Lab 2 - Recommend Products using ML with Cloud SQL and Dataproc
These are notes taken as part of the course " Google Cloud Platform Big Data and Machine Learning Fundamentals" And was practiced with Qwiklabs.

Here, we use Cloud SQL to store the data for the recommendation engine built on 	Spark ML. 

## Create a Cloud SQL instance
## Create tables, view data
## Data sourcing and load in to C SQL
## View the data
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
Run the ML model

## Explore inserted rows in Cloud SQL















<!--stackedit_data:
eyJoaXN0b3J5IjpbMTk3OTY4MzE1LDEzMDg0NDg5NzddfQ==
-->
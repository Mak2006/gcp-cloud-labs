1.  Lab 1  - creds
username
student-02-659d25a95a0e@qwiklabs.net
password
v6PQYdH7
Project id - 
qwiklabs-gcp-02-2b1da5da4031

2. create a vm - easy 
3. `cat /proc/cpuinfo` gives the info of the vm
4. Installing git 
`sudo apt-get update`
`sudo apt-get -y -qq install git`
`git --version`
5. Ingest the data - 
`git clone https://github.com/GoogleCloudPlatform/training-data-analyst`
6. Ingest code with LESS - what is it? It is man page viewer
`less $file`
7.  The bash prompt is too long `>> PS1=" myprompt>  "
8. ingest is basically, obtain the data - say like `wget http://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.csv -O earthquakes.csv`
9. just to get a snippet of it `head earthquakes.csv`
 
10.**transform the data**  - here we use python. this is done using ipynb
[https://github.com/GoogleCloudPlatform/datalab-samples/blob/master/basemap/earthquakes.ipynb](https://github.com/GoogleCloudPlatform/datalab-samples/blob/master/basemap/earthquakes.ipynb)
This is nice code which explains beautifully what is going on. 
10.  install python on the machine. 
given script does it.  Basilcally it did the following 
```
!/bin/bash

sudo apt-get update
sudo apt-get -y -qq --fix-missing install python3-mpltoolkits.basemap python3-numpy python3-matplotlib python3-requests

# Copyright 2016 Google Inc.
# Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at
# http://www.apache.org/licenses/LICENSE-2.0
# Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.
~                   
```
12.  then just calling the transform.py, actually this is the call to earthquake.ipynb and it is fantastic, it generates the png as well and is quite lean not like the java and spring hell. 
13. Next - gsutil to copy - `gsutil cp earthquakes.* gs://qwiklabs-gcp-02-2b1da5da4031/earthquakes/` |So this copies the files to the bucket, bucket was created prior to this. 
14.  Next we  go to the URL - 
https://storage.googleapis.com/qwiklabs-gcp-02-2b1da5da4031/earthquakes/earthquakes.htm

15. this lab was great. Python is great

## Lab 2

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTM5NzM2MjcxMF19
-->
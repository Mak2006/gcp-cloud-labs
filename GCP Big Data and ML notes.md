# GCP Big data and ML 
Notes from the GCP Big data and ML course

1. We spin a vm, do the calculation get the final artifact and store it on the storage.
2. In the cloud world - a way of thinking is storage is separated from compute. Both have different costs and serve different needs. Clusters are fungible, spin them use them and delete them.  Clusters are now temporary resources. Use scheduled deletion. Store the data off the cluster, only then they can be deleted or recreated. 
3. GCP projects is logical grouping, while zones etc are physical aspects.
4. GCP comes with Jupyter network. Very high performance network. This makes it possible to have compute wholly separated from storage. 
5. ML is training with examples != rule engine.	
6. How does recommendation engine work - reviews data - rating - results
7. 

## Cost optimization tips
3.  **VM** - stop the vm, and pay only for disks attached. VM running cost is more than disk attached cost.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIxMDg3OTYyNTIsMjAwNjUxNTEzNCwtNj
gxNjQ1ODE3LC01ODkxOTQzODQsLTE2NDkxNjczOTYsLTI0OTQ5
NDI0NSw4NTkzNzA3MSwtNTM1MTQ1ODU3XX0=
-->
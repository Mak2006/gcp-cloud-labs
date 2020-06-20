## Classify Images with Pre-built ML Models using Cloud Vision API and AutoML

**Business Driver**
Overview - In this lab, you will experiment with pre-built models so there's no coding. First we'll start with the pre-trained Vision API where we don't need to bring our own data and then we'll progress into AutoML for more sophisticated custom labelling that we need.

Functional - In this lab you will upload images to Cloud Storage and use them to train a custom model to recognize different types of clouds (cumulus, cumulonimbus, etc.). Then try to classify new images based on what is learnt. 

**Algo - Load >> Train >> Predict**

Quite a big lab!! undestandable due to AutoML training
![Whoops](https://i.imgur.com/qP8vnP6.png)

**Enable Auto ML**
This can be done a command or from dashboard. 

**Copy the data to a bucket**
`gsutil mb gs://{projid}}`

**Data set**
We require a labelled data set, showing the different clouds. This is available at  - `gsutil -m cp -r gs://automl-codelab-clouds/* {{bucket}}`

**Labelling the data set**
We have three diferent folders where the images are kept and we have an excel where all the files are present and the file has another column which has the label. However the file does not have the bucket name, and hence we have to alter the csv. 
`gsutil cp gs://automl-codelab-metadata/data.csv .`
`sed -i -e "s/placeholder/${BUCKET}/g" ./data.csv`
`gsutil cp ./data.csv gs://${BUCKET}`

So our image files and the `data.csv` 	is ready. This is all AutoML requires.

**Train - Auto ML **
Create a **Single-Label Classification**, give the csv

This is a test data set,  having small number of images. The images is divided into training, cross validation and prediction set.   
**What happens during training** Based on the training set it has done feature detection using multiple models and then selected the best one which worked best both on the training set and the cross validation set.  The model requires a name. 

**Train has a cost**
And Google gives a free tier for small training exercercises. This takes around 1 - 1.5 hours. 

**Evaluate the model **
Herer 


> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTA4NzI0MzU3NSwyMjgzNDgwODUsMTYwND
AzMTc4MSwtNzY0NDg3MzE0LDgwOTM2MjkyLC0xNDY3MDg2NjUx
LC0yMDczNzExNzFdfQ==
-->
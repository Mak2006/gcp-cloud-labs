## Classify Images with Pre-built ML Models using Cloud Vision API and AutoML

**Business Driver**
Overview - In this lab, you will experiment with pre-built models so there's no coding. First we'll start with the pre-trained Vision API where we don't need to bring our own data and then we'll progress into AutoML for more sophisticated custom labelling that we need.

Functional - In this lab you will upload images to Cloud Storage and use them to train a custom model to recognize different types of clouds (cumulus, cumulonimbus, etc.). Then try to classify new images based on what is learnt. 

**Algo - Load >> Train >> Predict**

Quite a big lab!! undestandable duet to AutoML init
![Whoops](https://i.imgur.com/qP8vnP6.png)

**Enable Auto ML**
This can be done a command or from dashboard. 

**Copy the data to a bucket**
`gsutil mb gs://{projid}}`






> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbODA5MjUwMDg4LC03NjQ0ODczMTQsODA5Mz
YyOTIsLTE0NjcwODY2NTEsLTIwNzM3MTE3MV19
-->
## Classify Images with Pre-built ML Models using Cloud Vision API and AutoML

**Business Driver**
Overview - In this lab, you will experiment with pre-built models so there's no coding. First we'll start with the pre-trained Vision API where we don't need to bring our own data and then we'll progress into AutoML for more sophisticated custom labelling that we need.

Functional - In this lab you will upload images to Cloud Storage and use them to train a custom model to recognize different types of clouds (cumulus, cumulonimbus, etc.). Then try to classify new images based on what is learnt. 

**Requirements -**  The training images (The training images are publicly available in a Cloud Storage bucket.) Labelling. 

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
Here we see the **Precision, Recall, Accuracy , Confusion matrix, Confidence threshold etc.**

**Sample confusion matrix** 

![](https://i.imgur.com/h1tinYL.png)


**Generate Predictions**
Now we are at our last part of the work. We feed the model an image and let it find the type.  **Test and Use** is used to upload the image and we check the results. 

### Results
Enable -
![](https://i.imgur.com/hk1ZyyH.png)
Bucket created
Invoke shell, copied image files
![](https://i.imgur.com/hVrrdzl.png)

Adopting a label file
![](https://i.imgur.com/3cQ3dbJ.png)

So we have to replace the placeholder, we do this using vi
`:%s/placeholder/buckname`
![](https://i.imgur.com/A7QzYe4.png)

Move this over to the bucket
![](https://i.imgur.com/YG7avDL.png)

 We cheked the bucket the file is now  there.
Next we go to Vision and create a dataset.  And give our data.csv file as the input set
![](https://i.imgur.com/boqgw5x.png)

Next it seems going to the "Images" tab imports the images
![](https://i.imgur.com/iApwNa1.png)

We ran into issues as the bucket was created in another region, hence we create a bucket in the us-central region and transfer the files.
`gsutil cp -r gs://qwiklabs-gcp-03-76e67ede2d2c/* gs://mb_qwiklabs-gcp-03-76e67ede2d2c/`

We cant rename the bucket hence we alter the data.csv.
Encountering issues, the "running importing images" seems t o take ages. 

Finally it completed
![](https://i.imgur.com/xYcfPaM.png)


It automatically selected the split for train and test
![](https://i.imgur.com/oByHMEe.png)

Train setting
![](https://i.imgur.com/j8pgbkb.png)

Training commences, it is 9.04 and about 1.46 left on the lab clock
![](https://i.imgur.com/xiEGi9c.png)

 9.27 still training
 10.06 completed
![](https://i.imgur.com/n9jC36v.png)

We evalate

![](https://i.imgur.com/DiZ0bpb.png)

Saved the confusion matrix

[Confusion Matrix](https://drive.google.com/file/d/1WG1Wp-MMtLBgBpbDEdUvZbZscYebENiB/view?usp=sharing)

![](https://i.imgur.com/SBdrvm0.png)

Adjusted the confidence threshold to 0.82
![](https://i.imgur.com/DrdkPpN.png)

Generate Predictions, uploaded the images
![](https://i.imgur.com/e4CA30y.png)

Using python to obtain the results
```
import sys

from google.cloud import automl_v1beta1
from google.cloud.automl_v1beta1.proto import service_pb2


# 'content' is base-64-encoded image data.
def get_prediction(content, project_id, model_id):
  prediction_client = automl_v1beta1.PredictionServiceClient()

  name = 'projects/{}/locations/us-central1/models/{}'.format(project_id, model_id)
  payload = {'image': {'image_bytes': content }}
  params = {}
  request = prediction_client.predict(name, payload, params)
  return request  # waits till request is returned

if __name__ == '__main__':
  file_path = sys.argv[1]
  project_id = sys.argv[2]
  model_id = sys.argv[3]

  with open(file_path, 'rb') as ff:
    content = ff.read()

  print get_prediction(content, project_id, model_id)
```
We execute the request with
`python predict.py YOUR_LOCAL_IMAGE_FILE 627335553411 ICN5123198618878083072`
![]()


Using REST API

Use your custom model
You can now run predictions on images using your custom vision model. You will need a service account.

The request.json
`{
  "payload": {
    "image": {
      "imageBytes": "YOUR_BASE64_ENCODED_IMAGE_BYTES"
    }
  }
}`


Execute the request
`$
curl -X POST -H "Content-Type: application/json" \
  -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
  https://automl.googleapis.com/v1beta1/projects/627335553411/locations/us-central1/models/ICN5123198618878083072:predict \
  -d @request.json`
> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTQyMTYwNTA5OCwxMDg5NTA4NzA5LC0xNj
E1Nzk3MDI4LC0yMDAwNDE3NjkyLDEzNDUwOTY4NDksMTU4NDQz
NDIyNiw4NDYyNjEyOSw3NzQ5ODI3NTYsMTM3NzYxMDI1NiwtNj
cwNjU5NjgwLC0xOTEyNDQwNjIzLDEwOTQxODcxODcsMTEyNTc1
ODg3NywyNTcxNDI3MzYsMTgxOTMxOTg0NCwyMjgzNDgwODUsMT
YwNDAzMTc4MSwtNzY0NDg3MzE0LDgwOTM2MjkyLC0xNDY3MDg2
NjUxXX0=
-->
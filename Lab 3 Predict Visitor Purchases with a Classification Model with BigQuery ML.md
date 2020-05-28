# Predict Visitor Purchases with a Classification Model with BigQuery ML

These are notes taken as part of the course " Google Cloud Platform Big Data and Machine Learning Fundamentals" And was practiced with Qwiklabs.
 
 [BigQuery Machine Learning](https://cloud.google.com/bigquery/docs/bigqueryml-analyst-start) (BQML, product in beta) is a new feature in BigQuery where data analysts can create, train, evaluate, and predict with machine learning models with minimal coding.

Scenario, you have a data set loaded and we use BigQuery to get some insights. This is plain SQL. 

To get the data in BQML we use 
[https://console.cloud.google.com/bigquery?p=data-to-insights&d=ecommerce&t=web_analytics&page=table](https://console.cloud.google.com/bigquery?p=data-to-insights&d=ecommerce&t=web_analytics&page=table)

### Query and explore the ecommerce dataset
**Sample query 1** -   Out of the total visitors who visited our website, what % made a purchase?
```
#standardSQL
WITH visitors AS(
SELECT
COUNT(DISTINCT fullVisitorId) AS total_visitors
FROM `data-to-insights.ecommerce.web_analytics`
),

purchasers AS(
SELECT
COUNT(DISTINCT fullVisitorId) AS total_purchasers
FROM `data-to-insights.ecommerce.web_analytics`
WHERE totals.transactions IS NOT NULL
)

SELECT
  total_visitors,
  total_purchasers,
  total_purchasers / total_visitors AS conversion_rate
FROM visitors, purchasers
```
Result 
![sample 1 results](https://i.imgur.com/S7agBwf.png)

**Sample 2 ** What are the top 5 selling products?
```
SELECT
  p.v2ProductName,
  p.v2ProductCategory,
  SUM(p.productQuantity) AS units_sold,
  ROUND(SUM(p.localProductRevenue/1000000),2) AS revenue
FROM `data-to-insights.ecommerce.web_analytics`,
UNNEST(hits) AS h,
UNNEST(h.product) AS p
GROUP BY 1, 2
ORDER BY revenue DESC
LIMIT 5;
```
Results Sample 2 - 
![Sample 2 results](https://i.imgur.com/Ea9zQ0g.png)

**Sample 3** How many visitors bought on subsequent visits to the website?

```
# visitors who bought on a return visit (could have bought on first as well
WITH all_visitor_stats AS (
SELECT
  fullvisitorid, # 741,721 unique visitors
  IF(COUNTIF(totals.transactions > 0 AND totals.newVisits IS NULL) > 0, 1, 0) AS will_buy_on_return_visit
  FROM `data-to-insights.ecommerce.web_analytics`
  GROUP BY fullvisitorid
) 
SELECT COUNT(DISTINCT fullvisitorid) AS total_visitors, will_buy_on_return_visit FROM all_visitor_stats GROUP BY will_buy_on_return_visit
```
Results Sample 3
![Sample 3 results](https://i.imgur.com/b2gzGPN.png)

Now we shall use BQML to do get more ML based insights
We ingest the data set, see th features available and preview the data. 

The BQML models
| Model          | Model Type   | Label Data type                                        | Example                                                           |
|----------------|--------------|--------------------------------------------------------|-------------------------------------------------------------------|
| Forecasting    | linear_reg   | Numeric value (typically an integer or floating point) | Forecast sales figures for next year given historical sales data. |
| Classification | logistic_reg | 0 or 1 for binary classification                       | Classify an email as spam or not spam given the context.          |

Before we can use BQML we choose two features to start with, these are  
-   `totals.bounces`  (whether the visitor left the website immediately)
-   `totals.timeOnSite`  (how long the visitor was on our website)
Using this we create the model and check how good it is using ROC metrics described later.

We first test the query in isolation which gives us these two vars. 
```
SELECT
  * EXCEPT(fullVisitorId)
FROM

  # features
  (SELECT
    fullVisitorId,
    IFNULL(totals.bounces, 0) AS bounces,
    IFNULL(totals.timeOnSite, 0) AS time_on_site
  FROM
    `data-to-insights.ecommerce.web_analytics`
  WHERE
    totals.newVisits = 1)
  JOIN
  (SELECT
    fullvisitorid,
    IF(COUNTIF(totals.transactions > 0 AND totals.newVisits IS NULL) > 0, 1, 0) AS will_buy_on_return_visit
  FROM
      `data-to-insights.ecommerce.web_analytics`
  GROUP BY fullvisitorid)
  USING (fullVisitorId)
ORDER BY time_on_site DESC
LIMIT 10;
```
The above returns the value of `will buy on return visit` is rule based and is not probabilistic. It cannot predict accurately.
The results
![feature search](https://i.imgur.com/9j65gIq.png)

**Features and Labels**  here the existing cols are the available features, and we have taken the two params as our features, the `will buy on return` is our label.

 It is here ML is useful. 
**The value of building a ML model is to get the probability of future purchase based on the data gleaned about their first session.**

### Create a dataset
Before launching the BQML we create a dataset.  We create the data set into the our project id. 
![ds creation](https://i.imgur.com/JJDnTPz.png)


### Create a training and evaluation dataset to be used for batch prediction

So we now train our ML on the data using the SQL query.  Note we amend the query to focus on certain period and not the whole data. 
We below create our BQML model qwiklabs-gcp-xxxxxxxxx:ecommerce.classification_model".
```
CREATE OR REPLACE MODEL `ecommerce.classification_model`
OPTIONS
(
model_type='logistic_reg',
labels = ['will_buy_on_return_visit']
)
AS

#standardSQL
SELECT
  * EXCEPT(fullVisitorId)
FROM

  # features
  (SELECT
    fullVisitorId,
    IFNULL(totals.bounces, 0) AS bounces,
    IFNULL(totals.timeOnSite, 0) AS time_on_site
  FROM
    `data-to-insights.ecommerce.web_analytics`
  WHERE
    totals.newVisits = 1
    AND date BETWEEN '20160801' AND '20170430') # train on first 9 months
  JOIN
  (SELECT
    fullvisitorid,
    IF(COUNTIF(totals.transactions > 0 AND totals.newVisits IS NULL) > 0, 1, 0) AS will_buy_on_return_visit
  FROM
      `data-to-insights.ecommerce.web_analytics`
  GROUP BY fullvisitorid)
  USING (fullVisitorId)
;
```
**Training commences**
![training commences](https://i.imgur.com/Cxt1NOz.png)
On running this our model is being trained on the data and we have our model with the features value populated.  We now do a prediction. 


### Check the generated model 
**Training finishes**
![enter image description here](https://i.imgur.com/EqEkPTs.png)

The model results
![enter image description here](https://i.imgur.com/cjr29ZA.png)


### Evaluate the performance of your machine learning model
For classification problems in ML, you want to minimize the False Positive Rate (predict that the user will return and purchase and they don't) and maximize the True Positive Rate (predict that the user will return and purchase and they do). This relationship is visualized with a ROC (Receiver Operating Characteristic).

**roc_auc** is just one of the performance metrics available during model evaluation. Also available are [accuracy, precision, and recall] (https://en.wikipedia.org/wiki/Precision_and_recall).

In BQML, **roc_auc** is simply a queryable field when evaluating your trained ML model.

### Compare the ROC structures
```
SELECT
  roc_auc,
  CASE
    WHEN roc_auc > .9 THEN 'good'
    WHEN roc_auc > .8 THEN 'fair'
    WHEN roc_auc > .7 THEN 'not great'
  ELSE 'poor' END AS model_quality
FROM
  ML.EVALUATE(MODEL ecommerce.classification_model,  (

SELECT
  * EXCEPT(fullVisitorId)
FROM

  # features
  (SELECT
    fullVisitorId,
    IFNULL(totals.bounces, 0) AS bounces,
    IFNULL(totals.timeOnSite, 0) AS time_on_site
  FROM
    `data-to-insights.ecommerce.web_analytics`
  WHERE
    totals.newVisits = 1
    AND date BETWEEN '20170501' AND '20170630') # eval on 2 months
  JOIN
  (SELECT
    fullvisitorid,
    IF(COUNTIF(totals.transactions > 0 AND totals.newVisits IS NULL) > 0, 1, 0) AS will_buy_on_return_visit
  FROM
      `data-to-insights.ecommerce.web_analytics`
  GROUP BY fullvisitorid)
  USING (fullVisitorId)

));
```

Results
![ROC existing](https://i.imgur.com/bWW49cF.png)

After evaluating your model you get a **roc_auc** of 0.72, which shows the model has not great, predictive power. Since the goal is to get the area under the curve as close to 1.0 as possible, there is room for improvement.


### Feature engineering to increasing the roc
We change the model to as below 
```
CREATE OR REPLACE MODEL `ecommerce.classification_model_2`
OPTIONS
  (model_type='logistic_reg', labels = ['will_buy_on_return_visit']) AS

WITH all_visitor_stats AS (
SELECT
  fullvisitorid,
  IF(COUNTIF(totals.transactions > 0 AND totals.newVisits IS NULL) > 0, 1, 0) AS will_buy_on_return_visit
  FROM `data-to-insights.ecommerce.web_analytics`
  GROUP BY fullvisitorid
)

# add in new features
SELECT * EXCEPT(unique_session_id) FROM (

  SELECT
      CONCAT(fullvisitorid, CAST(visitId AS STRING)) AS unique_session_id,

      # labels
      will_buy_on_return_visit,

      MAX(CAST(h.eCommerceAction.action_type AS INT64)) AS latest_ecommerce_progress,

      # behavior on the site
      IFNULL(totals.bounces, 0) AS bounces,
      IFNULL(totals.timeOnSite, 0) AS time_on_site,
      totals.pageviews,

      # where the visitor came from
      trafficSource.source,
      trafficSource.medium,
      channelGrouping,

      # mobile or desktop
      device.deviceCategory,

      # geographic
      IFNULL(geoNetwork.country, "") AS country

  FROM `data-to-insights.ecommerce.web_analytics`,
     UNNEST(hits) AS h

    JOIN all_visitor_stats USING(fullvisitorid)

  WHERE 1=1
    # only predict for new visits
    AND totals.newVisits = 1
    AND date BETWEEN '20160801' AND '20170430' # train 9 months

  GROUP BY
  unique_session_id,
  will_buy_on_return_visit,
  bounces,
  time_on_site,
  totals.pageviews,
  trafficSource.source,
  trafficSource.medium,
  channelGrouping,
  device.deviceCategory,
  country
);
```

We check the ROC now usin gthe query below . 
Note this ROC is different from the previous query. 
```
#standardSQL
SELECT
  roc_auc,
  CASE
    WHEN roc_auc > .9 THEN 'good'
    WHEN roc_auc > .8 THEN 'fair'
    WHEN roc_auc > .7 THEN 'not great'
  ELSE 'poor' END AS model_quality
FROM
  ML.EVALUATE(MODEL ecommerce.classification_model_2,  (

WITH all_visitor_stats AS (
SELECT
  fullvisitorid,
  IF(COUNTIF(totals.transactions > 0 AND totals.newVisits IS NULL) > 0, 1, 0) AS will_buy_on_return_visit
  FROM `data-to-insights.ecommerce.web_analytics`
  GROUP BY fullvisitorid
)

# add in new features
SELECT * EXCEPT(unique_session_id) FROM (

  SELECT
      CONCAT(fullvisitorid, CAST(visitId AS STRING)) AS unique_session_id,

      # labels
      will_buy_on_return_visit,

      MAX(CAST(h.eCommerceAction.action_type AS INT64)) AS latest_ecommerce_progress,

      # behavior on the site
      IFNULL(totals.bounces, 0) AS bounces,
      IFNULL(totals.timeOnSite, 0) AS time_on_site,
      totals.pageviews,

      # where the visitor came from
      trafficSource.source,
      trafficSource.medium,
      channelGrouping,

      # mobile or desktop
      device.deviceCategory,

      # geographic
      IFNULL(geoNetwork.country, "") AS country

  FROM `data-to-insights.ecommerce.web_analytics`,
     UNNEST(hits) AS h

    JOIN all_visitor_stats USING(fullvisitorid)

  WHERE 1=1
    # only predict for new visits
    AND totals.newVisits = 1
    AND date BETWEEN '20170501' AND '20170630' # eval 2 months

  GROUP BY
  unique_session_id,
  will_buy_on_return_visit,
  bounces,
  time_on_site,
  totals.pageviews,
  trafficSource.source,
  trafficSource.medium,
  channelGrouping,
  device.deviceCategory,
  country
)
));
```
the roc now is 


###  Predict which new visitors will come back and purchase
We are now in a position to use the model and predict

```
SELECT
*
FROM
  ml.PREDICT(MODEL `ecommerce.classification_model_2`,
   (

WITH all_visitor_stats AS (
SELECT
  fullvisitorid,
  IF(COUNTIF(totals.transactions > 0 AND totals.newVisits IS NULL) > 0, 1, 0) AS will_buy_on_return_visit
  FROM `data-to-insights.ecommerce.web_analytics`
  GROUP BY fullvisitorid
)

  SELECT
      CONCAT(fullvisitorid, '-',CAST(visitId AS STRING)) AS unique_session_id,

      # labels
      will_buy_on_return_visit,

      MAX(CAST(h.eCommerceAction.action_type AS INT64)) AS latest_ecommerce_progress,

      # behavior on the site
      IFNULL(totals.bounces, 0) AS bounces,
      IFNULL(totals.timeOnSite, 0) AS time_on_site,
      totals.pageviews,

      # where the visitor came from
      trafficSource.source,
      trafficSource.medium,
      channelGrouping,

      # mobile or desktop
      device.deviceCategory,

      # geographic
      IFNULL(geoNetwork.country, "") AS country

  FROM `data-to-insights.ecommerce.web_analytics`,
     UNNEST(hits) AS h

    JOIN all_visitor_stats USING(fullvisitorid)

  WHERE
    # only predict for new visits
    totals.newVisits = 1
    AND date BETWEEN '20170701' AND '20170801' # test 1 month

  GROUP BY
  unique_session_id,
  will_buy_on_return_visit,
  bounces,
  time_on_site,
  totals.pageviews,
  trafficSource.source,
  trafficSource.medium,
  channelGrouping,
  device.deviceCategory,
  country
)

)

ORDER BY
  predicted_will_buy_on_return_visit DESC;
```

The findings 

-   Of the top 6% of first-time visitors (sorted in decreasing order of predicted probability), more than 6% make a purchase in a later visit.
    
-   These users represent nearly 50% of all first-time visitors who make a purchase in a later visit.
    
-   Overall, only 0.7% of first-time visitors make a purchase in a later visit.
    
-   Targeting the top 6% of first-time increases marketing ROI by 9x vs targeting them all!
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTYzMDUzMzI3NCwtMTA4NjQxOTE1NiwtNj
I1MDc1Njk5LDE4MjgyMDMzODMsLTE0NzIwNDEwOCwyMDIwNDcw
MTMxLDEyMjAxNTgxODUsMTEyOTc5NDg3LC0yMTIxMDY0OTg2LD
ExNzQ0NzU2MzQsLTE1MjA5MDAyOTMsMTEyMTAwNzEzMiwtMTI0
MjMyNzUxMywtNjcwOTIyMzk1LDU2MzEyNTgyNiwyMDcxNDM2Mz
IsLTM3MzkyNjA5NywxODIyOTY5MjIzLC0xNDQ0MDg5NDU4XX0=

-->
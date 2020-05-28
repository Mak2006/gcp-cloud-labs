# Predict Visitor Purchases with a Classification Model with BigQuery ML

These are notes taken as part of the course " Google Cloud Platform Big Data and Machine Learning Fundamentals" And was practiced with Qwiklabs.
 
 [BigQuery Machine Learning](https://cloud.google.com/bigquery/docs/bigqueryml-analyst-start) (BQML, product in beta) is a new feature in BigQuery where data analysts can create, train, evaluate, and predict with machine learning models with minimal coding.

Scenario, you have a data set loaded and we use BigQuery to get some insights. This is plain SQL. 

### Query and explore the ecommerce dataset
**Sample query 1 **   Out of the total visitors who visited our website, what % made a purchase?
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

Now we shall use BQML to do get more ML based insights
We ingest the data set, see th features available and preview the data. 

The BQML models
| Model          | Model Type   | Label Data type                                        | Example                                                           |
|----------------|--------------|--------------------------------------------------------|-------------------------------------------------------------------|
| Forecasting    | linear_reg   | Numeric value (typically an integer or floating point) | Forecast sales figures for next year given historical sales data. |
| Classification | logistic_reg | 0 or 1 for binary classification                       | Classify an email as spam or not spam given the context.          |


We choose two features to ponder upon 
-   `totals.bounces`  (whether the visitor left the website immediately)
-   `totals.timeOnSite`  (how long the visitor was on our website)
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
The above returns the value of `will buy on return visit` as zero. It cannot predict. It is here ML is useful. 
**The value of building a ML model is to get the probability of future purchase based on the data gleaned about their first session.**

So we now train our ML on the data using the SQL query.  Note we amend the query to focus on certain period and not the whole data. 
We below qwiklabs-gcp-xxxxxxxxx:ecommerce.classification_model".
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


### Create a training and evaluation dataset to be used for batch prediction
Create data set , select BQML model, 
4. Create a classification (logistic regression) model in BQML
5. Evaluate the performance of your machine learning model
6. Predict and rank the probability that a visitor will make a purchase

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTA3MDY4ODM3NSwxMTc0NDc1NjM0LC0xNT
IwOTAwMjkzLDExMjEwMDcxMzIsLTEyNDIzMjc1MTMsLTY3MDky
MjM5NSw1NjMxMjU4MjYsMjA3MTQzNjMyLC0zNzM5MjYwOTcsMT
gyMjk2OTIyMywtMTQ0NDA4OTQ1OF19
-->
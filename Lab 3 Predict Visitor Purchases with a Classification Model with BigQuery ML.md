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

```
3. Create a training and evaluation dataset to be used for batch prediction
4. Create a classification (logistic regression) model in BQML
5. Evaluate the performance of your machine learning model
6. Predict and rank the probability that a visitor will make a purchase

<!--stackedit_data:
eyJoaXN0b3J5IjpbMzIzMTIwMjkwLC02NzA5MjIzOTUsNTYzMT
I1ODI2LDIwNzE0MzYzMiwtMzczOTI2MDk3LDE4MjI5NjkyMjMs
LTE0NDQwODk0NThdfQ==
-->
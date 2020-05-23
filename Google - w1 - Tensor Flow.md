## Google ML

1. Tell some uses 
    1. Automatic photo taggin.
    2.  Auto picture conversion 
    3. Text suggestion in email
    4. Rank brain
2. Usual hiccups in implementation 
    1. too much focus on data training 
    2. less on operationalization. 
    3. Using pretrained vs buildin your own.
    4. Want to do ML and there is no data.  
3.  Generic responses to ML 
    1. Multiple ML for a solution is required. 
    2. 2 stages  - A training and then inference
    3.  Framing an ML problem 
    4.  State of art - we do not have to create a model and train. MLaaS being available, pretrained models. 
4. **What is the new paradigm** - you train the program to learn and predict. Instead of you creating the program. This is less about data and more about replacing programming with ML.
    1. replacing heuristic rules by ML, that is what ML about. 
    2. **any thing for which you are writing rules today comes under the gamut of ML.** 
    3. 
5.  **Strategy of ML** 
     1. General steps
         1. Defining the KPI's
         2. Collecting data, data cleansing
         3. 3. Building infrastructure
         4. 4. Optimizing ML algorithm
         5. 5. Integration
    7. Take the org from no ML to ML 
          1. Individual contributor 
          2. Delgation team, convey the idea, get it ratified. 
          3. Digitization - archtiecture 
          4. Big data and analytics
          5.  ML 
6.  Different types of Bias in ML 
     1. the confusion matrix
    
|            | machine \+ve                  | machine \-ve                    |   |
|------------|-------------------------------|---------------------------------|---|
| label \+ve | true positive                 | false negative \(type 2 error\) |   |
| label \-ve | false positive \(type 1 error | true negative                   |   |
  1. False positive rate $= \frac {false -ve} {false -ve + true +ve}$
  2. False negative rate $$ $= \frac {false +ve} {false +ve + true +ve}$
  3. [https://en.wikipedia.org/wiki/Sensitivity_and_specificity](https://en.wikipedia.org/wiki/Sensitivity_and_specificity)

## Lab 1 to explore the data
1. Import a data and visualize them  
	1. Get a data into cloud storage, create a storage, get a shell and go into the shell, curl the data and upload. 
	2. `git clone https://github.com/GoogleCloudPlatform/training-data-analyst`

`# Create SQL query using natality data after the year 2000
query = """
SELECT
  weight_pounds,
  is_male,
  mother_age,
  plurality,
  gestation_weeks,
  FARM_FINGERPRINT(CONCAT(CAST(YEAR AS STRING), CAST(month AS STRING))) AS hashmonth
FROM
  publicdata.samples.natality
WHERE year > 2000
"""`

Next check with Big query 
`# Call BigQuery and examine in dataframe
from google.cloud import bigquery
df = bigquery.Client().query(query + " LIMIT 100").to_dataframe()
df.head()`

Next create a data frame 
`# Create function that finds the number of records and the average weight for each value of the chosen column
def get_distinct_values(column_name):
  sql = """
SELECT
  {0},
  COUNT(1) AS num_babies,
  AVG(weight_pounds) AS avg_wt
FROM
  publicdata.samples.natality
WHERE
  year > 2000
GROUP BY
  {0}
  """.format(column_name)
  return bigquery.Client().query(sql).to_dataframe()`


visualizatin - 
`# Bar plot to see is_male with avg_wt linear and num_babies logarithmic
df = get_distinct_values('is_male')
df.plot(x='is_male', y='num_babies', kind='bar');
df.plot(x='is_male', y='avg_wt', kind='bar');`

another plot 
`# Line plots to see mother_age with avg_wt linear and num_babies logarithmic
df = get_distinct_values('mother_age')
df = df.sort_values('mother_age')
df.plot(x='mother_age', y='num_babies');
df.plot(x='mother_age', y='avg_wt');`

`# Bar plot to see plurality(singleton, twins, etc.) with avg_wt linear and num_babies logarithmic
df = get_distinct_values('plurality')
df = df.sort_values('plurality')
df.plot(x='plurality', y='num_babies', logy=True, kind='bar');
df.plot(x='plurality', y='avg_wt', kind='bar');`

`# Bar plot to see gestation_weeks with avg_wt linear and num_babies logarithmic
df = get_distinct_values('gestation_weeks')
df = df.sort_values('gestation_weeks')
df.plot(x='gestation_weeks', y='num_babies', logy=True, kind='bar');
df.plot(x='gestation_weeks', y='avg_wt', kind='bar');`

## Other labs 1
Have downloaded all the notebooks ipynb and saved it 
	 
3.  
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTIzNTIzNDk0XX0=
-->
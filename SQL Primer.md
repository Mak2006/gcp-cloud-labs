### Links 

### Creating 
[link](https://github.com/GoogleCloudPlatform/training-data-analyst/blob/master/courses/data-engineering/demos/partition.md#create-a-date-partitioned-table-with-sql-ddl)
```
CREATE OR REPLACE TABLE ecommerce.partitions
 PARTITION BY date_formatted
 OPTIONS(
   description="a table partitioned by date"
 ) AS

SELECT
  COUNT(transactionId) AS total_transactions,
  PARSE_DATE("%Y%m%d", date) AS date_formatted
FROM
  `data-to-insights.ecommerce.all_sessions`
WHERE
  transactionId IS NOT NULL
GROUP BY date
```

### Select syntax master

```
SELECT
FUNC(COL_NAME)
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1NTUwNzMxMTYsNjIyOTY1MTExLC0yNT
AwMDEzNTYsMzE3MTA0NTE4XX0=
-->
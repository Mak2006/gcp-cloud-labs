### Links 
[Modern SQL](https://modern-sql.com/)

[15 minutes of history from Modern SQL](https://modern-sql.com/static/2018-11-DataNatives-Mother-Of-All-Query-Languages.w540.LjXFkxer.mp4)


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
FUNC(COL_NAME) AS RESULT_COL_NAME, 
...
FROM
TABLE_NAME_1, TABLE_NAME_2

WHERE
[CHECKS INVOLVING FIELD NAMES AND VALUES]
[JOIN IF MULTIPLE TABLE NAMES ARE THERE]

{GROUP BY
COL1,
COL2
}

{ORDER BY
COL3, ASC / DESC
}

{LIMIT 10}


```

### Different Func possible 
**Mathematical**
`precipitation/10 AS prcp`


### Where clause 
`id = '32'` a particular id
`AND` `OR` `NOT`  - AND 
`ISNULL`
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3MzQ4NTM0OTcsLTE1NTUwNzMxMTYsNj
IyOTY1MTExLC0yNTAwMDEzNTYsMzE3MTA0NTE4XX0=
-->
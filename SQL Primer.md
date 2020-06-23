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

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTcwNTkzMjAwLC0xNTU1MDczMTE2LDYyMj
k2NTExMSwtMjUwMDAxMzU2LDMxNzEwNDUxOF19
-->
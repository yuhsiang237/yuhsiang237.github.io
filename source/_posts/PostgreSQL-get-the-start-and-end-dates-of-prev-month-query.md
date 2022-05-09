---
title: PostgreSQL get the start and end dates of prev month query
date: 2022-05-05 10:19:01
categories:
  - [Database,PostgreSQL]
---

My table has below data, and I want to get the previous data of month,and today is 2022-05-04. 
```
2022-03-01
2022-04-01
2022-04-01
2022-05-03
```

This is the answer:

```sql
WITH TEMP_TABLE AS
(
    SELECT '2022-03-01'::TIMESTAMP WITHOUT TIME ZONE AS MYDATE
    UNION ALL
    SELECT '2022-04-01'::TIMESTAMP WITHOUT TIME ZONE AS MYDATE    
    UNION ALL
    SELECT '2022-04-02'::TIMESTAMP WITHOUT TIME ZONE AS MYDATE
    UNION ALL
    SELECT '2022-05-03'::TIMESTAMP WITHOUT TIME ZONE AS MYDATE
)
SELECT
  *
FROM 
  TEMP_TABLE TT
WHERE
  TT.MYDATE >= DATE_TRUNC('MONTH',NOW()) - INTERVEL '1 MONTH'
  AND TT.MYDATE < DATE_TRUNC('MONTH',NOW())
```
**explain**  
The function DATE_TRUNC will help us convert datetime to first date of month.
It's like Now() is 2022-05-09, and function DATE_TRUNC('MONTH',NOW()) will convert datetime to 2022-05-01.I use the DATE_TRUNC create a duration.

Example:
NOW() = 2022-05-09
```
x >= 2022-04-01 
x < 2022-05-01
```
So,the result is 2022-04-01~2022-04-30.


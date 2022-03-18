---
title: SQL Server 使用執行計畫進行SQL性能分析
date: 2022-03-18 15:06:00
categories:
  - [Database,SQL Server]
---
### 前言
最近因為排序分頁等問題要分析性能，因此就來記錄一下SQL性能分析。
在此使用的是SQL Server的SSMS的執行計畫去分析效能。

### 實作
1.先開啟執行計畫，就上面兩個黃色標起來的地方
{% asset_img "2.PNG" %}

2.再來執行SQL語法。  
```sql
SELECT 
    T.Id,
    T.Name,
    T.Priority,
    T.UserId,
    U.UserName
FROM Todos T
LEFT JOIN Users U
ON U.Id = T.UserId 
    AND U.IsValid = 1
WHERE 
    T.IsValid = 1 
    AND T.UserId = 3
ORDER BY T.Id
``` 

3.結果底下會出現一個區塊Execution Plan
{% asset_img "1.PNG" %}

4.查看每一個語句

5
{% asset_img "3.png" %}
{% asset_img "4.png" %}
{% asset_img "5.png" %}
{% asset_img "6.png" %}

(撰寫中...未完待續...)

### 參考資料
- https://docs.microsoft.com/zh-tw/sql/relational-databases/performance/display-an-actual-execution-plan?view=sql-server-ver15
- http://jengting.blogspot.com/2013/12/executionplan-keypoint.html
- http://caryhsu.blogspot.com/2011/06/sql-server.html
- https://www.itread01.com/content/1546162932.html
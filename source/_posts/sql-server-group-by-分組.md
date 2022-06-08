---
title: '[Day13] SQL server Group By 資料分組與聚合函數COUNT(), MAX(), MIN(), SUM(), AVG() - SQL Server資料庫入門'
date: 2021-09-13 04:31:26
categories:
  - [Topic,2021 SQL Server Challenge]
  - [Database,SQL Server]
---
在這回中，將要探討SQL server中的Group By用法，並搭配聚合函數使用。
資料在資料表中都是一列一列的，而分組則是把這些列的某些特徵欄位視為分組依據，並且將他們合為一組，並使用聚合函數就能夠做出簡易的統計。

### 測試資料
還是一樣，先放上資料表:

**customers 顧客**
{% asset_img "顧客.png" %}
**products 產品**
{% asset_img "產品.png" %}
**orders 出貨單**
{% asset_img "訂單.png" %}
**order_details 出貨單明細**
{% asset_img "明細.png" %}


### GROUP BY
把相同值列分組成匯總列。
Group By常常與COUNT(), MAX(), MIN(), SUM(), AVG()這些總計有關的語法一起用。

**聚合函數**
COUNT(column_name):取得欄位資料筆數。(ps.如果想統計整列不管有沒有空值問題可以下 COUNT(*) )
MAX(column_name):取得欄位最大值。
MIN(column_name):取得欄位最小值。
SUM(column_name):取得欄位加總。
AVG(column_name):取得欄位平均值。

語法:
```
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
ORDER BY column_name(s); 
```

實作:統計訂單明細的資料內容。
```
Select order_number,sum(price*amount) as 訂單總額 
,avg(price) as 平均商品單價,min(price) as 最低商品單價
,max(price) 最高商品單價,sum(amount) as 總貨量
,count(order_number) as 細項數量 
from order_details group by order_details.order_number
```
結果:
以order_number訂單編號為分組要件，把order_details表中所有重複的order_number都獨立識別成一個群組。
再以這個群組去統計資料。
如下圖，因為資料庫中order_details只有2筆資料，所以把這2筆的order_number嘗試分組，發現都只有A002這個訂單編號，於是就只分成了一組A002的資料，而這組A002資料底下則有2筆資料，可供統計聚合函數COUNT(), MAX(), MIN(), SUM(), AVG()使用。
{% asset_img "ex1.png" %}



**參考資料**
https://www.w3schools.com/sql/sql_groupby.asp
https://ithelp.ithome.com.tw/articles/10218537
https://ithelp.ithome.com.tw/articles/10218055
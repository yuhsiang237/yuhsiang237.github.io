---
title:  '[Day17] SQL server having用法 - SQL Server資料庫入門'
date: 2021-09-16 20:40:49
categories:
  - [Topic,2021 SQL Server鐵人賽]
  - [Database,SQL Server]
---
在SQL查詢時對這個關鍵詞有印象，但非常陌生。
於是這次就來探討一下having。
他用來跟我們前面聊過的Group by搭配，因為where不能用在聚合函數

### having
HAVING 子句是用來取代 WHERE 搭配聚合函數 (aggregate function) 進行條件查詢，因為 WHERE 不能與聚合函數一起使用。

聚合函數指的也就是 AVG()、COUNT()、MAX()、MIN()、SUM() 等這些內建函數。

語法:
```
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
HAVING condition
ORDER BY column_name(s);
```

### 實作

先列一下目前資料表狀態

**customers 顧客**
{% asset_img "顧客.png" %}
**products 產品**
{% asset_img "產品.png" %}
**orders 出貨單**
{% asset_img "訂單.png" %}
**order_details 出貨單明細**
{% asset_img "明細.png" %}


實作:統計訂單明細並得到總額大於50000的訂單。
```
Select order_number,sum(price*amount) as 訂單總額 
from order_details group by order_details.order_number
having sum(price*amount) > 50000
```
結果:
透過having來與聚合函數搭配。
{% asset_img "1.png" %}

### 總結
發現having用法也很直覺，沒想像中難。
最主要就是group by後想篩選資料時就使用它。


**參考資料**
https://www.fooish.com/sql/having.html
https://www.w3schools.com/sql/sql_having.asp
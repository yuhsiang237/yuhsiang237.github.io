---
title: '[Day16] SQL server exists用法 - SQL Server資料庫入門'
date: 2021-09-16 12:47:44
categories:
  - [Topic,2021 SQL Server Challenge]
  - [Database,SQL Server]
---
這回探討SQL server exists用法。
之前有在子查詢的範例中使用到，但覺得不夠清楚所以拉出來探討看看。

### Exists
EXISTS 運算子可以連接子查詢，用來判斷子查詢是否有返回的結果，如果有結果返回則為真、否則為假。若 EXISTS 為真，就會繼續執行外查詢中的 SQL；若 EXISTS 為假，則整個 SQL 查詢就不會返回任何結果。

語法
```
SELECT column_name(s)
FROM table_name
WHERE EXISTS
(SELECT column_name FROM table_name WHERE condition); 
```
且exists跟in語法都可以達到相同效果。
如:
```
SELECT * FROM table_a
WHERE EXISTS
(SELECT * FROM table_b WHERE table_b.id=table_a.id);
```
等價於
```
SELECT * FROM table_a
WHERE id
in (SELECT id FROM table_b);
```
注意使用EXISTS條件,main query(外部查詢)和subquery(子查詢)一定要有連接,例如 table_b.id=table_a.id,如果沒有連結EXISTS條件使用是無效的

### 實作

**customers 顧客**
{% asset_img "顧客.png" %}
**products 產品**
{% asset_img "產品.png" %}
**orders 出貨單**
{% asset_img "訂單.png" %}
**order_details 出貨單明細**
{% asset_img "明細.png" %}


實作:尋找有下訂單的客戶
```
Select * from customers 
where exists (select * from orders where customers.number = orders.customer_number )
```
結果:
把結果符合customers.number = orders.customer_number的列都返回。
所以得到有下訂單的客戶。
此外發現exists子查詢的Select * from其實隨便指派一個欄位都可以，因為並不影響結果。
{% asset_img "1.png" %}

實作:尋找"沒"有下訂單的客戶
```
Select * from customers 
where not exists (select * from orders where customers.number = orders.customer_number )
```
結果:
在exists前加個not可以得到相反結果，即沒下訂單的客戶
{% asset_img "2.png" %}

### 總結
覺得還是用in會比較直覺，用exists時會突然覺得where竟然能直接接一個非欄位的用法。
但熟悉後可能又覺得還好了。

此外，似乎有in跟exists效能一說，目前就先記著，改日有空再找機會打一篇。

**參考資料**
https://www.w3schools.com/sql/sql_exists.asp
https://www.fooish.com/sql/exists.html
http://mylinoraclesql.blogspot.com/2013/07/exists.html
https://eeluck.pixnet.net/blog/post/27559378
---
title: '[Day14] SQL server in與not in - SQL Server資料庫入門'
date: 2021-09-16 11:45:33
categories:
  - [SQL]
---
在這回中將探討SQL server的in與not in 用法。


### IN 與 NOT IN

1.可以使用在where中揀選多個值
2.其實就是多個or的簡化寫法

有點像是以下的感覺:

where 欄位值 in ('A','B','C')
欄位值=='A' or 欄位值=='B' or 欄位值=='C'

where 欄位值 not in ('A','B','C')
欄位值!='A' or 欄位值!='B' or 欄位值!='C'


1.給予明確數值，篩選是否在這些值中。
```
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1, value2, ...); 
```
2.可以用在子查詢上，篩選是否在子查詢的數值中
```
SELECT column_name(s)
FROM table_name
WHERE column_name IN (SELECT STATEMENT); 
```

### 範例實作
在此，提供一下目前資料表中的測試資料，作為核對。

**customers 顧客**
{% asset_img "顧客.png" %}
**products 產品**
{% asset_img "產品.png" %}
**orders 出貨單**
{% asset_img "訂單.png" %}
**order_details 出貨單明細**
{% asset_img "明細.png" %}

### 實驗

實作:想取得產品名稱為PS5和PS4的產品。
```
Select * from products where name in ('PS5','PS4')
```
結果:

{% asset_img "1.png" %}

實作:想取訂單明細中不包含PS4的訂單。
```
Select * from order_details 
where product_number not in (Select number from products where name = 'PS4' )
```
結果:
後方的子查詢結果是['P002']，之後因為條件下not in表示是不包含P002，not in ('P002')。
所以得到不含PS4的訂單明細。
{% asset_img "2.png" %}

### 結論
其實就把in拆成多個or去理解就好。

**參考資料**
https://www.w3schools.com/sql/sql_in.asp
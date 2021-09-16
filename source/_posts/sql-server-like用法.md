---
title: '[Day15] SQL server like用法 - SQL Server資料庫入門'
date: 2021-09-16 12:22:56
categories:
  - [SQL]
---
在這回中將探討SQL server中like的使用方式。

### Like
like被用在SQL語法中的where條件。
用來篩選模糊字串。

通常搭配這兩種運算子去實作模糊搜尋條件。

**%** 代表零或一個或更多的字符
**_** 代表一個字符

語法:
```
SELECT column1, column2, ...
FROM table_name
WHERE columnN LIKE pattern; 
```

常見的模糊字串操作:
<table class="ws-table-all notranslate">
  <tbody><tr>
    <th>LIKE Operator</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>WHERE CustomerName LIKE 'a%'</td>
    <td>Finds any values that start with "a"</td>
  </tr>
  <tr>
    <td>WHERE CustomerName LIKE '%a'</td>
    <td>Finds any values that end with "a"</td>
  </tr>
  <tr>
    <td>WHERE CustomerName LIKE '%or%'</td>
    <td>Finds any values that have "or" in any position</td>
  </tr>
  <tr>
    <td>WHERE CustomerName LIKE '_r%'</td>
    <td>Finds any values that have "r" in the second position</td>
  </tr>
  <tr>
    <td>WHERE CustomerName LIKE 'a_%'</td>
    <td>Finds any values that start with "a" and are at least 2 characters in length</td>
  </tr>
  <tr>
    <td>WHERE CustomerName LIKE 'a__%'</td>
    <td>Finds any values that start with "a" and are at least 3 characters in length</td>
  </tr>
  <tr>
    <td>WHERE ContactName LIKE 'a%o'</td>
    <td>Finds any values that start with "a" and ends with "o"</td>
  </tr>
</tbody></table>


### 範例實作

**customers 顧客**
{% asset_img "顧客.png" %}
**products 產品**
{% asset_img "產品.png" %}
**orders 出貨單**
{% asset_img "訂單.png" %}
**order_details 出貨單明細**
{% asset_img "明細.png" %}


實作:想取得產品名稱為P開頭的產品。
```
Select * from products where name like 'P%'
```
結果:
{% asset_img "1.png" %}


實作:實作訂單收或簽名為「X中間有一個字X」的人
```
Select * from orders where orders.customer_signature like 'X_X'
```
結果:
可得到XXX這個人的訂單明細。因為他X與X間隔一個字符。
{% asset_img "2.png" %}

### 結論
可以用like在資料庫內做簡易的模糊比對，將一些無法直接對應的數值用這種方式去篩選。

**參考資料**
https://www.w3schools.com/sql/sql_like.asp
---
title:  '[Day9] SQL server查詢語法Select - SQL Server資料庫入門'
date: 2021-09-09 00:54:08
categories:
  - [Topic,2021 SQL Server Challenge]
  - [Database,SQL Server]
---

### 前言
在上回我們手動對資料庫填充了一些基礎資料:{% post_link 'SQL-server手動寫入資料' '[Day8] SQL server手動寫入資料 - SQL Server資料庫入門'%}
在這回中我們要利用建立好的資料庫來練習查詢語法。

### 目前表中資料
先看一下資料庫中的資料:
**customers 顧客**
{% asset_img "顧客.png" %}
**products 產品**
{% asset_img "產品.png" %}
**orders 出貨單**
{% asset_img "訂單.png" %}
**order_details 出貨單明細**
{% asset_img "明細.png" %}

### SQL SELECT 語法實踐

select為資料庫查詢的語法，被用來選取資料表中的資料。
當語法執行後，資料會被儲存在一個結果表(result table)，即結果集(result-set)。

**1.選擇表中的部分欄位**
```
SELECT column1, column2, ...
FROM table_name; 
```
實作:查詢orders中的編號、寄送日、寄送地址
```
Select number,shipping_date,shipping_address from orders
```
結果:
{% asset_img "select01.png" %}
**2.選擇表中的全部欄位**
```
SELECT * FROM table_name; 
```
實作:查詢orders中所有欄位
```
Select * from orders
```
結果:
{% asset_img "select02.png" %}

### SQL SELECT DISTINCT 語法實踐
被用來取得非重複列的集合。
```
SELECT DISTINCT column1, column2, ...
FROM table_name; 
```
實作:查詢order_details的order_number，並回傳非重複的列
```
Select DISTINCT order_number from order_details 
```
結果:
{% asset_img "select distinct01.PNG" %}

實作:查詢order_details的order_number、id，並回傳非重複的列
```
Select DISTINCT id,order_number from order_details 
```
結果:
會發現跟上面的只搜尋order_number多增加一列，因為id與order_number這樣視為一列
{% asset_img "select distinct02.PNG" %}

### SQL WHERE 語法
用可來限制Select的條件、過濾資料。
可使用的限制條件:
<table class="ws-table-all notranslate">
  <tbody><tr>
    <th style="width:20%">Operator</th>
    <th style="width:70%">Description</th>
  </tr>
  <tr>
    <td>=</td>
    <td>Equal</td>
  </tr>
  <tr>
    <td>&gt;</td>
    <td>Greater than</td>
  </tr>
  <tr>
    <td>&lt;</td>
    <td>Less than</td>
  </tr>
  <tr>
    <td>&gt;=</td>
    <td>Greater than or equal</td>
  </tr>
  <tr>
    <td>&lt;=</td>
    <td>Less than or equal</td>
  </tr>
  <tr>
    <td>&lt;&gt;</td>
    <td>Not equal. <b>Note:</b> In some versions of SQL this operator may be written as !=</td>
  </tr>
  <tr>
    <td>BETWEEN</td>
    <td>Between a certain range</td>
  </tr>
  <tr>
    <td>LIKE</td>
    <td>Search for a pattern</td>
  </tr>
  <tr>
    <td>IN</td>
    <td>To specify multiple possible values for a column</td>
 
  </tr>
</tbody></table>

語法:

```
SELECT column1, column2, ...
FROM table_name
WHERE condition; 
```
實作:查詢order_details，並且限制單價為10000的資料
```
Select * from order_details  where price = '10000'
```
結果:
{% asset_img "where.PNG" %}

實作:查詢order_details，並且限制產品編號為P001、P002的結果

```
Select * from order_details  where product_number IN ('P001','P002')
```

結果:
{% asset_img "where2.PNG" %}

### SQL AND, OR and NOT 語法
在使用where時，我們可以用AND、 OR、 NOT去組合我們理想的條件。
AND:組合多個條件，全為TURE回傳
```
SELECT column1, column2, ...
FROM table_name
WHERE condition1 AND condition2 AND condition3 ...; 
```
OR:組合多個條件，符合其中一個條件都算在資料集中，最後回傳。
```
SELECT column1, column2, ...
FROM table_name
WHERE condition1 OR condition2 OR condition3 ...; 
```
NOT:想排除某條件
```
SELECT column1, column2, ...
FROM table_name
WHERE NOT condition; 
```

實作:查詢order_details，並且限制amount為2或total為30000的結果

```
Select * from order_details where amount = '2' or total = '30000'
```

結果:
{% asset_img "or.PNG" %}

實作:查詢order_details，並且限制amount為2或total為30000，且id不為2的結果

```
Select * from order_details where amount = '2' or total = '30000' and NOT id = '2'
```


結果:
{% asset_img "not.PNG" %}

### SQL ORDER BY 語法
可用來排序Select結果表的結果集合，透過欄位可以遞增、遞減。
```
SELECT column1, column2, ...
FROM table_name
ORDER BY column1, column2, ... ASC|DESC; 
```

實作:查詢order_details，並把id遞減排序

```
Select * from order_details order by id desc
```

結果:
{% asset_img "order.PNG" %}

### SQL TOP 語法
取得前幾筆資料，在MSSQL中則是用TOP；MYSQL用LIMIT，似乎每個DB都有不同的標準。
```
SELECT TOP number|percent column_name(s)
FROM table_name
WHERE condition; 
```
實作:查詢order_details，前一筆資料

```
Select TOP 1 * from order_details 
```

結果:
{% asset_img "top.PNG" %}

### SQL NULL Values 語法
可以用來篩選欄位是否為空值(未填充)。
為空:
```
SELECT column_names
FROM table_name
WHERE column_name IS NULL; 
```
不為空:
```
SELECT column_names
FROM table_name
WHERE column_name IS NOT NULL; 
```

實作:查詢order_details，remarks不為空的結果

```
Select * from order_details where remark IS NOT NULL
```

結果:
{% asset_img "null.PNG" %}


### 小結
在這回中大量練習了表單的查詢，發現其實還蠻單純的，並不會特別難或什麼。
在下回中會開始練習跨表的搜尋JOIN。

**參考資料**
https://ithelp.ithome.com.tw/articles/10214938
https://www.w3schools.com/sql/sql_select.asp
https://www.w3schools.com/sql/sql_where.asp
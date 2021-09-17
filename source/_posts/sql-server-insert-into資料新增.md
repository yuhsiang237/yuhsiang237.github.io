---
title: '[Day19] SQL server insert into 資料新增 - SQL Server資料庫入門'
date: 2021-09-17 08:38:56
categories:
  - [SQL]
---
在前面已經把大量的常見查詢語法做過一次實踐了。
接下來就是比較簡單的語法了，這回講的是insert into用來在表中插入資料。

### INSERT INTO
```
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...); 
```

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


實作:想要新增一筆客戶資料。
```
insert into customers (number,name,tel) values('U003','Tom','09-15965423')
```
結果:
{% asset_img "1.png" %}

### 透過子查詢新增資料
算是另一種少見的做法，就是數值的部分是使用查詢來的數值。
雖然很少用，但就稍微嘗試一下這種寫法:

實作:我想把products的number、name、price添加到customers將他們視為number、name、tel。
```
insert into customers (number,name,tel) 
select number,name,price from products 
```
結果:
就把products的資料加到customers了。(為了示範需求，因為真的超怪。
{% asset_img "2.png" %}

### 總結
簡易的操作了資料插入的語法。
下回就是更新update。

**參考資料**
https://ithelp.ithome.com.tw/articles/10220002
https://www.w3schools.com/sql/sql_insert.asp
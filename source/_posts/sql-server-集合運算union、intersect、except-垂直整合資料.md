---
title: '[Day12] 集合運算union、intersect、except 垂直整合 - SQL Server資料庫入門'
date: 2021-09-12 13:48:48
categories:
  - [SQL]
---
在先前我們有使用過的多表查詢都是水平整合的概念，如JOIN {% post_link 'sql-server-合併查詢join' '[Day10] SQL server Select合併查詢JOIN用法 - SQL Server資料庫入門'%} 。
以及上篇的子查詢 {% post_link '建立C-MVC專案' '[Day11] SQL server 子查詢 - SQL Server資料庫入門 '%} 
而這回，將要探討垂直整合這塊，資料庫提供聯集(union)、交集(intersect)、差集(except)語法可以實作這概念。

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
### UNION 聯集(剔除重複)
聯集
被用來結合多個Select語句的結果集(result set)。
與UNION ALL差別在"會剔除重複"，有重複的資料只會顯示其中一筆

**限制:**
1.欄位的屬性必須一樣
2.每個select語句都必須有相同的欄位數
3.select語句有多個欄位時，欄位的屬性要符合順序

語法:
```
SELECT column_name(s) FROM table1
UNION 
SELECT column_name(s) FROM table2; 
```

實作:想要把products的price與order_details的price結合，且剔除重複。
```
select price from products
union 
select price from order_details
```
結果:
{% asset_img "ex1.png" %}

### UNION ALL 聯集(不剔除重複)
聯集
與UNION差別在"不剔除重複"，有重複的資料會顯示多筆
```
SELECT column_name(s) FROM table1
UNION ALL
SELECT column_name(s) FROM table2; 
```

實作:想要把products的price與order_details的price結合，且不剔除重複。
```
select price from products
union all
select price from order_details
```
結果:
{% asset_img "ex2.png" %}


## INTERSECT 交集
交集，將select語句的結果集合併後找出相同的值然後返回結果集。
附註:沒有INTERSECT ALL這種用法
語法:
```
SELECT column_name(s) FROM table1
INTERSECT 
SELECT column_name(s) FROM table2; 
```

實作:取出有訂過訂單的客戶編號，把customers的number與orders的customer_number做交集。
```
select number from customers
intersect
select customer_number from orders
```
結果:
{% asset_img "ex3.png" %}


## EXCEPT 差集
差集，將select語句的結果集合併後找出不相同的值然後返回結果集。
**[補充]** EXCEPT及只適用於 SQL Server，Oracle的交集則是使用 MINUS，使用方法與EXCEPT一樣; 而MySQL不支援EXCEPT及MINUS，則要使用left join。可參考:http://www.geeksengine.com/database/multiple-table-select/minus-except.php
語法:
```
SELECT column_name(s) FROM table1
EXCEPT 
SELECT column_name(s) FROM table2; 
```

實作:取出"沒有"訂過訂單的客戶編號，把customers的number與orders的customer_number做差集。
```
select number from customers
except
select customer_number from orders 
```
結果:
{% asset_img "ex4.png" %}


### 小結
發現蠻多細節的語法在不同資料庫的名稱、寫法又會不一樣。
如果不熟某一資料庫，只能盡可能查，否則就得在程式裡面做(雖然較耗記憶體)。
而本篇中練習了聯集(union)、交集(intersect)、差集(except)，在使用上蠻直覺的，沒有太困難的操作語法，只要注意一些欄位的屬性排列不要排錯就好。
而我回顧以前，就是使用ORM(Object Relational Mapping)，雖然有提供語法，但卻在Select時很難直覺寫出語句，有時甚至不支援還要變成半原生寫法。
所以，以個人經驗覺得原生的SQL語法比較好懂@@。
然後，下篇預告是GROUP BY分組的用法。
**參考資料**
https://www.w3schools.com/sql/sql_union.asp
https://ithelp.ithome.com.tw/articles/10216761
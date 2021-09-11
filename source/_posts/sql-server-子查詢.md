---
title: '[Day11] SQL server 子查詢 - SQL Server資料庫入門'
date: 2021-09-11 18:47:01
categories:
  - [SQL]
---
在上回 {% post_link 'sql-server-合併查詢join' '[Day10] SQL server Select合併查詢JOIN用法 - SQL Server資料庫入門'%} 中，我們練習了合併查詢的JOIN。

在這回中，我們主要淺談一下子查詢。
他跟JOIN一樣都可以使用在多表的查詢上。

### 子查詢
子查詢通常使用在Select的Where子句上。
首先處理的是子查詢條件，才會向外處理外部的主查詢。有點像是寫一個function，然後回傳一個結果再給主程式去跑。

語法:
```
SELECT 欄位名稱1,欄位名稱2,...,欄位名稱n 
FROM 資料表名稱1
WHERE 欄位名稱 = 
(SELECT 欄位名稱 FROM 資料表名稱2 WHERE 條件)
```
1.子查詢要在括號()中。
2.通常子查詢SELECT只會取得單一欄位的值，以便主查詢的欄位進行比較運算。
3.如需排序，子查詢不能使用ORDER BY，只能使用GROUP BY 子句。
4.如果子查詢可以取得多筆資料，在主查詢需使用IN邏輯運算子。

在WHERE使用子查詢主要目的是取得其他資料表紀錄的欄位值來建立WHERE句的條件運算式。

### 實際演練
### 目前表中資料
在此，提供一下目前資料表中的測試資料，作為核對。

**customers 顧客**
{% asset_img "顧客.png" %}
**products 產品**
{% asset_img "產品.png" %}
**orders 出貨單**
{% asset_img "訂單.png" %}
**order_details 出貨單明細**
{% asset_img "明細.png" %}


實作:想要取得名子為XXX的訂單。
```
Select * from orders 
where orders.customer_number = (Select number from customers where customers.name = 'XXX' )
```
結果:
子查詢的語句只會返回單一一筆的number叫做U001，之後就會變成orders.customer_number = 'U001'去搜尋，最後得到結果。

{% asset_img "ex1.png" %}



實作:想要取得大於平均值的商品列表。
```
Select *,(select avg(price) from products) as avg_price from products 
where price > (select avg(price) from products)
```
結果:
原本想要寫成 where price > avg_price，但發現這樣的寫法不可以，因為前面的這種匿名欄位(原本就不在資料表中的欄位)在where後沒辦法取得，所以又補了一段(select avg(price) from products)。
總之，子查詢也可以用在欄位上。
{% asset_img "ex2.png" %}

實作:想取得訂單明細中有商品PS4及PS5的明細。
[補充]EXISTS指令:可以判斷子查詢的結果有沒有回傳資料，若有則會執行查詢中的結果。
```
Select * from order_details 
where exists 
(Select * from products where (products.name = 'PS4' or products.name ='PS5') and products.number = order_details.product_number)
```
結果:
搜尋order_details表單後用Exists子查詢，用Select取出商品名稱為PS4或PS5的集合，並且這集合的產品編號在訂單明細的產品編號裡。
{% asset_img "ex3.png" %}

實作:想取得還沒有下過訂單的顧客。
[補充]IN指令可以檢查是否存在子查詢的資料紀錄中。
```
Select * from customers 
where customers.number not in (Select orders.customer_number from orders )
```
結果:
用子查詢去查orders資料表，得到客戶編號的集合。之後主查詢用not in去篩選不在集合中的顧客編號。
最終得到U002這個沒下訂單的顧客。
{% asset_img "ex4.png" %}

### 小結
在這篇中練習了子查詢的寫法，並且知道子查詢可以放在欄位、或where後，且可以搭配如exists、in等等做法去擴展用法。
可能會找一時間把一些擴展語法如exists、in等等類型的都拉出來獨立講一遍。
下篇預計會是垂直的UNION查詢的做法。

**參考資料**
https://ithelp.ithome.com.tw/articles/10219497
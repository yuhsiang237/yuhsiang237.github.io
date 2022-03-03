---
title: '[Day10] SQL server Select合併查詢JOIN用法 - SQL Server資料庫入門'
date: 2021-09-10 22:31:26
categories:
  - [Topic,2021 SQL Server鐵人賽]
  - [Database,SQL Server]
---
### 前言
在上回 {% post_link 'sql-server查詢語法Select' '[Day9] SQL server查詢語法Select - SQL Server資料庫入門'%} 中，我們練習了一些查詢語法，且都是針對同一張表的查詢。
而在這回中，我們將要開始活用關聯式資料庫的核心用法「JOIN」，透過它實踐合併的查詢。

在這回中我們主要探討五種查詢，並實際來操作:

1.inner Join 內部合併查詢
2.left (outer) Join 左外部合併查詢
3.right (outer) Join 右外部合併查詢
4.full (outer) Join 完全外部合併查詢
5.cross Join 交叉合併查詢


### 目前表中資料
在此，提供一下目前資料表中的測試資料，作為核對。

**關聯圖**
{% asset_img "拉關聯完成.png" %}

**customers 顧客**
{% asset_img "顧客.png" %}
**products 產品**
{% asset_img "產品.png" %}
**orders 出貨單**
{% asset_img "訂單.png" %}
**order_details 出貨單明細**
{% asset_img "明細.png" %}


### Inner Join  內部合併查詢
內部合併查詢，為兩個表中都有的值才把資料拉出來。
如下圖，就是A、B的交集。
{% asset_img "1.png" %}
語法:
```
SELECT column_name(s)
FROM table1
INNER JOIN table2
ON table1.column_name = table2.column_name;
```

實作:對customers以及orders做inner join，得到出貨單與顧客關聯的資料集。
ps.稍微補充一下 orders.number as orders_number 是代表把某東西用as設定別稱

```
Select customers.number,customers.name,orders.number as orders_number 
from customers 
inner join orders 
on customers.number = orders.customer_number
```
結果:
可以發現，原本在order表內看不到的客戶名稱name出現了。且customers表中的編號U002並沒出現在表裡，因為在orders表中它找不到相同的值。
{% asset_img "範例1.PNG" %}

### Left (outer) Join 左外部合併查詢
取回左邊資料表的所有紀錄，並拉進右邊資料表的值。結果會以左邊資料表為主。

{% asset_img "2.png" %}
語法:
```
SELECT column_name(s)
FROM table1
LEFT JOIN table2
ON table1.column_name = table2.column_name;
```

實作:想要取得顧客名單同時也想知道他們有關的出訂單，對customers以及orders做left join。
```
Select customers.number,customers.name,orders.number as orders_number 
from customers
left join orders 
on customers.number = orders.customer_number
```
結果:
可以發現，customers為主表，所以有客戶即使沒出貨單也被拉出來，而U001這位客戶因為關連到兩筆所以列出兩列。

{% asset_img "範例2.PNG" %}

### Right (outer) Join 右外部合併查詢
取回右邊資料表的所有紀錄，並拉進左邊資料表的值。結果會以右邊資料表為主。
其實就是跟左外部查詢一樣，只是表顛倒放而已，通常左邊比較直覺。

{% asset_img "3.png" %}
語法:
```
SELECT column_name(s)
FROM table1
RIGHT JOIN table2
ON table1.column_name = table2.column_name;
```

實作:想要取得訂單，並嘗試抓出顧客名單即使客戶名單消失或為NULL，對customers以及orders做right join。
```
Select customers.number,customers.name,orders.number as orders_number 
from customers 
right join orders 
on customers.number = orders.customer_number
```
結果:
可以發現，orders為主表列出了所有訂單2筆A002、A003，並嘗試關聯到顧客。

{% asset_img "範例3.PNG" %}

### Full (outer) Join 完全外部合併查詢
full Join(完全外部合併)可以取回左右兩邊資料表的所有紀錄。

{% asset_img "4.png" %}
語法:
```
SELECT column_name(s)
FROM table1
FULL OUTER JOIN table2
ON table1.column_name = table2.column_name
WHERE condition; 
```
實作:訂單、顧客名單的左右資料全部合併
```
Select customers.number,customers.name,orders.number as orders_number 
from customers 
full join orders 
on customers.number = orders.customer_number
```
結果:
跟inner join比較的話，就是原本因為orders資料表沒有相關資料消失的U002客戶出現了。
{% asset_img "範例4.PNG" %}

### Cross Join 交叉合併查詢
交叉連接為兩個資料表間的笛卡兒乘積 (Cartesian product)，兩個資料表在結合時，不指定任何條件，即將兩個資料表中所有的可能排列組合出來
語法:
```
Select * From 表A Cross Join 表B;
```
也就是說表A有5筆紀錄，表B有8筆紀錄，交查合併後會得到5 * 8=40筆的紀錄，如果沒有指名欄位，欄位數就是2個資料表的欄位總數。

實作:取得訂單、顧客名單的所有可能排列組合
```
Select customers.number,customers.name,orders.number as orders_number 
from customers 
cross join orders 
```
結果:
customers目前兩筆資料、orders目前兩筆資料 2x2 = 4 所有可能排列組合。
因為沒用ON去指定，所以會發現顧客U002明明沒訂單卻關聯上了A002、A003訂單。
{% asset_img "範例5.PNG" %}

### 多組JOIN寫法
簡單來說就是可以依照你的目標去操作各種JOIN得到你要的結果。
大概畫一下圖:
{% asset_img "5.png" %}
實作:我想得到訂單詳細資料打平的樣子
ps.這種多組JOIN可以用as簡短，以下是全部簡寫。如果為了求好讀可以多打寫字，可以不要用as。
```
Select * from order_details as a
inner join orders  as b on a.order_number = b.number
inner join customers as c on c.number = b.customer_number
inner join products as d on a.product_number = d.number
```
結果:
其實就是把所有的主鍵、外來鍵拉起來而已R。就完全成為正規化前的模樣XDD
{% asset_img "N JOIN.PNG" %}

### 總結

在這回中學習了合併查詢的用法JOIN，一般來說都是搭配主鍵、外來鍵去做這些事。
而平常自己實際習慣用INNER JOIN、LEFT JOIN，而FULL JOIN、CROSS JOIN則超少用到。
也許是不太直覺的關係吧XD?
且因為需求都是人想出來的，如果不是計算機專業，一般很少人會想出FULL JOIN、CROSS JOIN。

然後預告一下，下回是子查詢。@@

**參考資料:**
https://ithelp.ithome.com.tw/articles/10215741
https://www.fooish.com/sql/cross-join.html
https://charleslin74.pixnet.net/blog/post/460066978
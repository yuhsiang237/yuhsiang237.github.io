---
title: '[Day18] SQL server ANY與ALL用法 - SQL Server資料庫入門'
date: 2021-09-16 21:00:25
categories:
  - [Topic,2021 SQL Server鐵人賽]
  - [Database,SQL Server]
---
在這回中要介紹我很少很少很少用到的any與all用法。
因為大部分搜尋條件都能靠前面介紹的那些組合度過了，並且更直覺。

### ANY
用於子查詢
任意，一個條件符合就返回true
```
SELECT column_name(s)
FROM table_name
WHERE column_name operator ANY
  (SELECT column_name
  FROM table_name
  WHERE condition); 
```


### ALL
用於子查詢
所有，全部條件滿足才返回true
在select、having、where都可能用到
```
SELECT column_name(s)
FROM table_name
WHERE column_name operator ALL
  (SELECT column_name
  FROM table_name
  WHERE condition); 
```

### 差異比較
使用 > 比較運算子來作例子，>ALL 代表大於每個數值；換句話說，就是大於最大值。例如， >ALL (1, 2, 3) 代表大於 3。>ANY 代表至少大於一個數值，也就是說大於最小值。因此 >ANY (1, 2, 3) 代表大於 1。

### 實際演練
還是一樣，先放上資料表:

{% asset_img "test.png" %}
*因為這次範例太難用原本訂單的表，於是建立一個測試的表


實作:在test表中比較類型為1數值大於所有類型為2的數值結果。
```
select * from test where type = '1' and number > all (select number from test where type = '2')
```
結果:
1的內容:[1,3,4,8]
2的內容:[2,5,4]
而以上方條件all等於找最大值，即number > 5 的結果。
{% asset_img "1.png" %}



實作:在test表中比較類型為1數值至少大於一個類型2數值的結果。
```
select * from test where type = '1' and number > any (select number from test where type = '2')
```
結果:
1的內容:[1,3,4,8]
2的內容:[2,5,4]
而以上方條件any，等於找最小值，即number > 2 的結果。
{% asset_img "2.png" %}

### 總結
用到機會連要擠出範例都很難@@...
之前的範例都能用原本建的資料表示範，而這回測試any、all時發現除非改動表的設計，否則要做出好的實驗數據太不容易了。
總之，all、any就只要求看懂就好。

**參考資料**
http://www.blueshop.com.tw/board/FUM20041006152735ZFS/BRD200309251806228LO.html
https://blog.csdn.net/xianglingchuan/article/details/51737330
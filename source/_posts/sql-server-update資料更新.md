---
title: '[Day20] SQL server update 資料更新 - SQL Server資料庫入門'
date: 2021-09-17 09:05:56
categories:
  - [SQL]
---
在這回簡易示範SQL server資料更新。

### UPDATE
主要就是用update搭配set去設定資料數值。
此外，如果要針對某一條件修改請用where加上條件，否則將會每筆都改會很悲劇。(如果你又沒備份的話，記得每日備份阿。)

語法:
```
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition; 
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


實作:想要更新客戶number為U002資料，把tel更新成09-00000000。
```
update customers
set tel='09-00000000'
where customers.number = 'U002'
```
結果:
{% asset_img "1.png" %}


### 結論
目前還沒犯過不小心更新全部表的失誤。但不知道哪天會很雖遇到(?)
所以，建議操作SQL前或是每日操作前都先備份，並且留存備份檔案，至少哪天有問題時還有機會救。
因為可能會有備份需求，所以先記著，之後會打篇資料備份。

下一回預計就是練習刪除語法。

**參考資料**
https://www.w3schools.com/sql/sql_update.asp
https://ithelp.ithome.com.tw/articles/10220040

---
title: '[Day21] SQL server delete 資料刪除 - SQL Server資料庫入門'
date: 2021-09-17 09:32:56
categories:
  - [Topic,2021 SQL Server Challenge]
  - [Database,SQL Server]
---
在這回簡易示範SQL server資料刪除。

### DELETE
主要就是用delete搭配某表，以及條件。
此外，要注意條件要記得下，不然整張表資料被刪掉會很悲劇。

語法:
```
DELETE FROM table_name WHERE condition; 
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


實作:想要刪除產品PS4。
```
delete from products where name = 'PS4'
```
結果:
{% asset_img "1.png" %}


### 結論
成功的練習了DELETE，非常簡單的語法。
但這種反而**要小心**，DELETE跟UPDATE，因為如果where條件下錯，整張表就完了QQ。

(ps.目前我還沒想到如果沒備份要怎麼救，只能求助google了。)

下回預計是資料庫備份。

**參考資料**
https://www.w3schools.com/sql/sql_delete.asp
https://ithelp.ithome.com.tw/articles/10220412
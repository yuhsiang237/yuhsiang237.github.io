---
title: '[Day8] SQL server手動寫入資料 - SQL Server資料庫入門'
date: 2021-09-08 00:05:59
tags:
---
在上回 {% post_link 'sql-server資料庫關聯' '[Day7] SQL server資料庫關聯 - SQL Server資料庫入門'%} 中，我們拉完了訂單表單的資料庫關聯。
在這回就要實際的將資料寫入。
### 訂單圖
{% asset_img "訂單.png" %}
### 資料表關聯圖

{% asset_img "拉關聯完成.png" %}

(Ps.如果你想知道怎麼把訂單圖轉成那四張資料表可以看 {% post_link 'SQL-server資料庫正規化' '
[Day5] 規劃資料表，透過資料庫正規化 - SQL Server資料庫入門
' %} )

我們預計將填入以下資料:
{% asset_img "資料表修正.png" %}


### 將資料手動寫入 

1.customers表 > 右鍵 Edit Top 200 Rows 叫出編輯視窗
{% asset_img "1點選客戶表.png" %}
2.將資料輸入即可，就像Excel那樣
{% asset_img "2寫入.png" %}
3.products表也一樣
{% asset_img "3產品.png" %}
4.orders表也一樣
{% asset_img "4訂單表.png" %}
5.order_details表也一樣，而id設定自動遞增，所以不用填寫會自動補。
{% asset_img "5明細.png" %}
6.這樣就就完成把資料手動寫入了。

### 結語
這回主要是練習將資料手動寫入，並看看是否會有問題。
再來就是SQL語法的撰寫了，會以比較常見>進階>特殊這樣去前進。
下回預計是用語法寫入的話該怎麼寫。







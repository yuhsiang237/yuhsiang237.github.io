---
title: '[Day26] SQL server 分頁處理 - SQL Server資料庫入門'
date: 2021-09-17 17:09:56
categories:
  - [Topic,2021 SQL Server鐵人賽]
  - [Database,SQL Server]
---
通常在寫表單時，我們不會一次索取所有資料，因為資料若有百萬筆，那效能可能會不好、浪費流量。
所以，這時候就需要分頁了。
但是SQL server沒有Mysql的limit功能，所以要自己組出來QQ。

稍微查了一下Google，發現已有不少現成做法，就當作紀錄記下來。

他的做法其實就是用子查詢。
就是內層先做一個有row欄位，這row欄位透過 ROW_NUMBER() OVER 去產生順序編號
製作好內部的子查詢後，再由外部把這子查詢視為一張temp_table
之後就可以用where去判斷想取哪段的row了。

但是我比較好奇的是，如果是很多left join、inner join好像用這方法會變得很亂。(?)

要:1~5筆資料。
```
SELECT * FROM ( 
 SELECT *, ROW_NUMBER() OVER (ORDER BY id desc)  row 
 FROM test
 ) as temp_table
WHERE row >= 1 and row <= 5
```
結果:
{% asset_img "1.png" %}

要:6~10筆資料。
```
SELECT * FROM ( 
 SELECT *, ROW_NUMBER() OVER (ORDER BY id desc)  row 
 FROM test
 ) as temp_table
WHERE row >= 6 and row <= 10

```
結果:
{% asset_img "2.png" %}



**參考資料**
http://bioankeyang.blogspot.com/2013/03/ms-sql-servermysql-limit.html
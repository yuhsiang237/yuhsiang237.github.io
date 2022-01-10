---
title: SQL Server Stored Procedure 介紹&實作
date: 2022-01-10 12:18:54
categories:
  - [SQL]
---
在這篇文章中簡易的介紹Stored Procedure。

### Stored Procedure(預存程序)
A stored procedure is a prepared SQL code that you can save, so the code can be reused over and over again.

So if you have an SQL query that you write over and over again, save it as a stored procedure, and then just call it to execute it.

You can also pass parameters to a stored procedure, so that the stored procedure can act based on the parameter value(s) that is passed.

簡單來說就是一種預存程序，寫在資料庫裡面，可以藉由外部傳入參數去呼叫數值，而得到結果。

### 優點
1.能夠讓不同應用程式直接呼叫，核心邏輯不用分散。
(但目前有微服務架構能改用API了，因此除非是特別有必要，否則還是很少碰到)

### 缺點
1.撰寫因為沒辦法中斷點Debug所以除錯困難。

### 簡易寫一個Stored Procedure
1.建立一段searchProduct的Stored Procedure。用來查詢特定Id的商品。
```
CREATE PROCEDURE searchProduct 
    @productId int
AS 
	SELECT * from Products 
	where Id = @productId
```

2.執行後可以在資料庫的「資料庫>Programmability>Stored Procedures」裡面找到

{% asset_img "1.png" %}
3.執行
```
exec searchProduct 6
```
即可得到結果:
{% asset_img "2.png" %}

4.這樣就完成了一段最簡單的Stored Procedure。
但在實作上主要維護時會很花時間，如果是千行T-SQL，無法使用中斷點會像是天書那樣。

### 總結
以目前寫過系統來說都是在後端程式裡面寫，而Stored Procedure算是一個新的體驗。
除非，真的有必要需求，否則還是用API式輕量化，因為比較好DEBUG。
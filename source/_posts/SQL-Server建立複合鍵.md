---
title: SQL Server 建立複合鍵(composite primary key)
date: 2022-05-06 10:44:34
categories:
  - [Database,SQL Server]
---
在這篇簡易紀錄一下SQL複合鍵的做法。
```sql
CREATE TABLE Orders(
	OrderNumber NVARCHAR(50),
	OrderType TINYINT,
	CreatedOn DATETIME,
	UpdatedOn DATETIME,
	IsValid BIT,
	Remarks NVARCHAR(100) NULL
	PRIMARY KEY (OrderNumber, OrderType)
)
```
就是在PRIMARY KEY加入兩個鍵值即可。
在此以OrderNumber、OrderType，表示同一訂單類型的編號不可重複。

如果是在SQL Server的介面設計上，就是點CTRL把需要設定的PK給匡列，之後再右鍵設定PK即可。
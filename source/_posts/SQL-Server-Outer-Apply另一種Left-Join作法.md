---
title: SQL Server Left Join只取第一筆做法Outer Apply
date: 2022-03-03 15:08:51
categories:
  - [Database,SQL Server]
---
### 前言
最近剛好有要在SQL Server中Left Join N筆結果排序後只取得第一筆的需求。
發現Left Join資料結果會有問題，所以發現了Outer Apply作法能夠達到，因此筆記一下。

### 實作

需求:
撈出每位使用者還沒完成的最早一筆任務。
並以Priority做排序，如果遇到相同層級則以最早新建的資料為主。


Todos資料表:
{% asset_img "2.PNG" %}

Users資料表:
{% asset_img "3.PNG" %}

sql:
~~~~sql
SELECT 
	A.UserName,
	B.Name TaskName,
	B.Id TaskId,
	B.Priority
FROM Users A
	OUTER APPLY
	(
		SELECT TOP 1 
			Name,Priority,Id
		FROM Todos 
		WHERE 
			A.Id = UserId 
			AND Todos.IsValid = 1 
			AND IsComplete = 0
		ORDER BY Priority ASC,Id ASC 
	) B
WHERE
	A.IsValid = 1
~~~~
執行結果:
{% asset_img "1.PNG" %}

### 總結
SQL Server有些特殊語法，有時真的得在實作時才會真的碰到。(坑)
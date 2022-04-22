---
title: SQL Server PARTITION BY 分割分組語法
date: 2022-04-22 15:34:24
categories:
  - [Database,SQL Server]
---
### 前言
最近處理到一些複雜需求，使用到了PARTITION BY。  
於是記錄一下PARTITION BY的用法。  
主要是能在分組時取得第一筆資料、與分組有關的操作。  


### PARTITION BY
Divides the query result set into partitions. The window function is applied to each partition separately and computation restarts for each partition.

PARTITION BY 可將查詢結果集分成幾個資料分割。

語法:
```sql
OVER ( [ PARTITION BY value_expression ] [ order_by_clause ] )  
```

### 實作

目標:要取得每個以UserId分組的第一筆資料

**資料表:[DBO].[TODOS]**
{% asset_img "1.PNG" %}

首先先使用PARTITION製造出分割出群組編號

```sql
SELECT 
	ROW_NUMBER() OVER(PARTITION BY USERID ORDER BY ID) ROW_ID,
	*
FROM 
	[DBO].[TODOS]
```
可以見到ROW_ID出現在列表中，即代表這是這個UserId組的第幾筆資料。
依序為:  
UserId為1的第1筆資料  
UserId為3的第1筆資料  
UserId為3的第2筆資料  
UserId為3的第3筆資料  
UserId為4的第1筆資料
UserId為5的第1筆資料  

{% asset_img "2.PNG" %}

再來只要把這結果集外部包一個搜尋，使用條件就能拿到第一筆資料，如下設定:
ROW_ID為1代表取得每個分組的第一筆資料。

```sql
SELECT * FROM (
	SELECT 
		ROW_NUMBER() OVER(PARTITION BY USERID ORDER BY ID) ROW_ID,
		*
	FROM 
		[DBO].[TODOS]
) TEMP
WHERE TEMP.ROW_ID = 1
```
最終成功拿到每個UserId分組的第一筆資料
{% asset_img "3.PNG" %}

### 參考資料
- https://docs.microsoft.com/en-us/sql/t-sql/queries/select-over-clause-transact-sql?view=sql-server-ver15
- https://docs.microsoft.com/zh-tw/sql/t-sql/queries/select-over-clause-transact-sql?view=sql-server-ver15
- https://ithelp.ithome.com.tw/articles/10225653
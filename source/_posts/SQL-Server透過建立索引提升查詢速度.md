---
title: SQL Server透過建立索引提升查詢速度
date: 2022-05-31 13:25:37
categories:
  - [Database,SQL Server]
  - [Topic,巨量資料]
---
因為最近遇到資料庫量大的瓶頸，因此稍微紀錄一下調效能的作法建立「索引」。
之前有提到過: {% post_link 'SQL-Server-叢集索引vs非叢集索引' 'SQL Server 叢集索引vs非叢集索引'%} 可以先看這邊。

而這回主要透過建立索引去改善查詢效能，如下實作:

### 實作
1.首先先建立一張表
```sql
USE [TodoDB]
CREATE TABLE [Todos](
	[Id] [int] IDENTITY(1,1) NOT NULL,
	[Name] [nvarchar](100) NULL,
	[Priority] [tinyint] NULL,
	[UserId] [int] NOT NULL,
	[IsValid] [bit] NOT NULL,
	[CreatedOn] [datetime] NOT NULL,
	[UpdatedOn] [datetime] NOT NULL
	CONSTRAINT [PK_Todos] PRIMARY KEY CLUSTERED ([Id] ASC)
	);
GO
```

2.增加幾筆資料
```sql
USE [TodoDB]
INSERT INTO [Todos] 
(
	[Name],
	[Priority],
	[UserId],
	[IsValid],
	[CreatedOn],
	[UpdatedOn]
)
VALUES
    ('測試1',1,1,1,'2022-05-31','2022-05-31'),
    ('測試2',4,2,1,'2022-05-31','2022-05-31'),
    ('測試3',3,3,1,'2022-05-31','2022-05-31'),
    ('測試4',2,1,1,'2022-05-31','2022-05-31'),
    ('測試5',1,1,1,'2022-05-31','2022-05-31')
GO
```

3.建立非叢集索引
```sql
USE [TodoDB]
	CREATE NONCLUSTERED INDEX IX_Priority
	ON [Todos](Priority ASC,Id ASC)
GO
```
這時如過資料量筆數大
進行查詢剛好排序了Priority ASC、Id ASC速度就會提升。


### 參考資料
- https://docs.microsoft.com/en-us/sql/relational-databases/indexes/create-nonclustered-indexes?view=sql-server-ver16
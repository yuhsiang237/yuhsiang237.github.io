---
title: Sql Server 查詢資料分頁使用offset fetch next
date: 2022-04-08 10:20:26
tags:
---
撰寫中...

```sql
SELECT *
FROM [TodoDB].[dbo].[Todos]
ORDER BY [ID] DESC
OFFSET 0 ROWS 
FETCH NEXT 10 ROWS ONLY
```
### 參考資料
https://blog.csdn.net/hou973561160/article/details/84313681
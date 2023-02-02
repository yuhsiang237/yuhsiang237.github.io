---
title: Improve SQL speed by adding index in SQL Server
date: 2023-02-02 16:00:42
categories:
  - [Database,SQL Server]
---
In the case, I have a simple sql about select, which sorts by date, <code>OrderDate</code>. At the same time, there are **5,000,000 records** in the table, Orders. Due to the large amount of data, the SQL speed becomes very slow, about 6 seconds.

```sql
SELECT TOP 1000 [Id]
      ,[CreatedOn]
      ,[UpdatedOn]
      ,[IsValid]
      ,[OrderDate]
      ,[OrderNumber]
FROM [OrderDB].[dbo].[Orders]
ORDER BY [OrderDate] DESC
```
After running the sql script, the speed of the result was very slow, about 6 seconds.  
{% asset_img "ScriptSpeed.png" %}

So to imporve this problem, I add the index,  <code>NONCLUSTERED INDEX</code>.

```sql
USE OrderDB;  
GO  
CREATE NONCLUSTERED INDEX [IX_Orders_OrderDate]  
    ON Orders ([OrderDate]);   
GO 
```

After running the script, I found the speed has been improved by index, about 0 seconds, and there're still **5,000,000 records** in the table.

{% asset_img "ScriptSpeedImporve.png" %}


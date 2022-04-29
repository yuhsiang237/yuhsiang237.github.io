---
title: SQL Server 使用GUID當作PK範例
date: 2022-04-28 15:16:43
categories:
  - [Database,SQL Server]
---

使用GUID需要將型別設定為uniqueidentifier。預設值通常會為NEWID()，即從資料庫生成時自動產生GUID帶入。

1.建立Table
```sql
CREATE TABLE Users (
    [GUID] uniqueidentifier NOT NULL DEFAULT NEWID(),
    [Name] NVARCHAR(50) NOT NULL,
    CONSTRAINT [PK_Users] PRIMARY KEY CLUSTERED ([GUID] ASC)
);
```

2.再來新增資料
```sql
INSERT INTO Users
(
    [Name]
)
VALUES
    ('MC HOTDOG'),
    ('張震嶽')
```

3.查詢
```sql
SELECT * FROM Users
```

結果:
```
GUID	Name
FB5AFFC7-0802-47F7-AFB3-531BC83B6C26	張震嶽
F8122471-70AE-43C5-9C40-A65ED54FACD5	MC HOTDOG
```

### 總結
使用GUID優點:
1.不會讓資料庫造成依賴ex:產生唯一識別碼
2.不用擔心鍵值重複(機率極低)ex:不同時間產的多資料表合成一張資料表
3.不好被猜測ex:F8122471-70AE-43C5-9C40-A65ED54FACD5
缺點:
1.空間使用GUID 16Bytes比整數的4Bytes多。
2.效能(待釐清，似乎眾說紛紜)

### 參考資料
- https://www.mssqltips.com/sqlservertip/6002/what-is-a-guid-in-sql-server/
- https://blog.darkthread.net/blog/guid-as-pk-on-db/
- https://iter01.com/540198.html
- https://stackoverflow.com/questions/11938044/what-are-the-best-practices-for-using-a-guid-as-a-primary-key-specifically-rega

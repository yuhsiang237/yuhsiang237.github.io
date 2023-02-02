---
title: Generate the test data in SQL Server by stored procedure example
date: 2023-02-02 14:34:44
categories:
  - [Database,SQL Server]
---
In the case, I want to create a simple script about creating the test data of 100000 records in SQL Server store procedure.

Today, I have a table, Orders, and I want to generate the test data to it.
``` sql
USE [OrderDB]
GO
/****** Object:  Table [dbo].[Orders]    Script Date: 2/2/2023 3:14:07 PM ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Orders](
	[Id] [int] IDENTITY(1,1) NOT NULL,
	[CreatedOn] [datetime] NULL,
	[UpdatedOn] [datetime] NULL,
	[IsValid] [bit] NULL,
	[OrderDate] [datetime] NULL,
	[OrderNumber] [varchar](50) NULL,
 CONSTRAINT [PK_Orders] PRIMARY KEY CLUSTERED 
(
	[Id] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON, OPTIMIZE_FOR_SEQUENTIAL_KEY = OFF) ON [PRIMARY]
) ON [PRIMARY]
GO
```
Then I write a simple stored procedure.
You can control the data count you want to generate.

If I want to create the 100000 records, I can do this.

```sql
use OrderDB
go
execute dbo.CreateTestData 100000
```

Below is the complete script, I wrote.
```sql
USE [OrderDB]
GO
/****** Object:  StoredProcedure [dbo].[CreateTestData]    Script Date: 2/2/2023 2:46:33 PM ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
ALTER PROCEDURE [dbo].[CreateTestData] @Number int
AS
BEGIN
	DECLARE
		@Counter int = 1
	WHILE @Counter <= @Number
	BEGIN
		INSERT INTO Orders (OrderDate,OrderNumber,IsValid,CreatedOn,UpdatedOn) 
		VALUES 
		(
			DATEADD(DAY, ABS(CHECKSUM(NEWID()) % 3650), '2000-01-01'),
			CONVERT(CHAR(8), FORMAT(GetDate(),'yyyyMMdd')) + right('00000000'+cast(@Counter as varchar(8)),8),
			1,
			GETDATE(),
			GETDATE()
		)
		SET @Counter= @Counter + 1
	END
END
```
The stored procedure inserts a random date into <code>OrderDate</code> and a sequence number into <code>OrderNumber</code>.


```
Id	CreatedOn	UpdatedOn	IsValid	OrderDate	OrderNumber
1	2023-02-02 15:27:27.763	2023-02-02 15:27:27.763	1	2002-11-30 00:00:00.000	2023020200000001
2	2023-02-02 15:27:27.763	2023-02-02 15:27:27.763	1	2007-04-29 00:00:00.000	2023020200000002
3	2023-02-02 15:27:27.763	2023-02-02 15:27:27.763	1	2002-03-18 00:00:00.000	2023020200000003
4	2023-02-02 15:27:27.763	2023-02-02 15:27:27.763	1	2003-03-27 00:00:00.000	2023020200000004
5	2023-02-02 15:27:27.763	2023-02-02 15:27:27.763	1	2007-11-07 00:00:00.000	2023020200000005
...
```
---
title: Simplify queries in SQL Server using CTE
date: 2023-02-03 15:21:45
categories:
  - [Database,SQL Server]
---
If you use queries in SQL, you could involve complex statements that could cause some maintenance or read problems. You can still do them, but you can choose a new way, <code>CTE</code>.


**What is CTE?**  
The common table expression (CTE) is a powerful construct in SQL that helps simplify a query.

With CTE, you can split the query into different temporary tables and use them.

### Example
If I want to create a report about the total amount of the user's order, I could use CTE to simplify it.

**[dbo].[Orders]**
```
OrderNumber	UserId	Amount
2023020300000001	1	500
2023020300000002	3	100
2023020300000003	1	300
2023020300000004	2	400
2023020300000005	1	100
```

**[dbo].[Users]**
```
Id	Name	Age
1	CK	30
2	RR	29
3	NC	25
```

Then I use the CTE to create a temp table and join them.

Result:

```
Id	Name	Age	OrderTotalAmount
1	CK	30	900
2	RR	29	400
3	NC	25	100
```
SQL:
```sql
USE OrderDB
GO
WITH Order_User_Total_CTE (UserId, OrderTotalAmount)
AS
-- Define the User Order Total CTE query.
(
    SELECT UserId, SUM(Amount) AS OrderTotalAmount
    FROM Orders
    GROUP BY UserId
)
-- Query
SELECT 
	U.*, 
	OUTC.OrderTotalAmount
FROM Users U
LEFT JOIN Order_User_Total_CTE OUTC ON U.Id = OUTC.UserId
GO
```

Now, if I need to create complex reports, summaries or stored procedures , I will use CTE to help me.
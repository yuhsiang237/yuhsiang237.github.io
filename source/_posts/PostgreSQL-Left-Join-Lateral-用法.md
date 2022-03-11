---
title: PostgreSQL Left Join Lateral 用法
date: 2022-03-10 16:50:57
categories:
  - [Database,PostgreSQL]
---
### 前言
最近碰到一個PostgreSQL搜尋與句的問題。
就是怎樣在PostgreSQL達到類似SQL Server的Outer Apply的寫法。
因為需要在原本一對一的表，再去LEFT JOIN一個一對多資料。
並且把一對多資料取得一個結果存進一對一的表，像是Boolean、數量。

目標:
一張是訂單主表，一張是訂單明細表
想以主表為主，取得有幾張明細表數量。
Table如下:

**Table ORDER**
```
SEQ
---- 
1
2
```

**Table ORDER_DETAIL**
```
SEQ  | ORDER_SEQ | NAME 
---------------------------
1    | 1         | 訂單明細1
2    | 2         | 訂單明細2
3    | 2         | 訂單明細3
```

**使用 Lateral :**
```sql
SELECT 
    O.SEQ AS ORDER_SEQ,
    RS.ORDER_DETAIL_COUNT
FROM ORDER AS O
LEFT JOIN LATERAL(
    SELECT COUNT(OD.SEQ) AS ORDER_DETAIL_COUNT FROM  ORDER_DETAIL AS OD 
    WHERE   OD.ORDER_SEQ = O.SEQ
) AS RS ON TRUE
```

**結果**
```
ORDER_SEQ | ORDER_DETAIL_COUNT
------------------------------
1         | 1
2         | 2
```


**不用 Lateral:**
```sql
SELECT 
    O.SEQ AS ORDER_SEQ,
    RS.ORDER_DETAIL_COUNT
FROM ORDER AS O
LEFT JOIN ORDER_DETAIL AS OD on OD.ORDER_SEQ = O.SEQ
GROUP BY O.SEQ
```

**結果**
```
ORDER_SEQ | ORDER_DETAIL_COUNT
------------------------------
1         | 1
2         | 2
```

### 總結
用LATERAL會比較直覺，就不用最後再去GROUP BY。
如果想繼承上面LEFT JOIN表的條件，那LATERAL優勢就會出來，會顯得簡單很多。

### 參考資料
- https://www.cnblogs.com/ricklz/p/12122808.html
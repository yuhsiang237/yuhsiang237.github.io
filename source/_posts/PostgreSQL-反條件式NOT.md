---
title: PostgreSQL 反條件式NOT
date: 2022-03-16 14:16:36
categories:
  - [Database,PostgreSQL]
---
### 前言
最近遇到要添加反例的案件，需要把整個條件反著寫。
但AND/OR反著寫就很違反閱讀習慣，於是找到了能用NOT這語句去概括整條件句，結果就可以變反。

如果有一表如下:
```
price_record
id | price
---+------
1  | 100 
2  | 200
3  | 300
```

IN:數值在這集合裡面。
```sql
SELECT * FROM PRICE_RECORD PR 
WHERE NOT 
    (
        -- 寫原本要反例的判斷邏輯
        PR.PRICE IN ('100','200')
    ) 
```

結果:
```
price_record
id | price
---+------
3  | 300
```






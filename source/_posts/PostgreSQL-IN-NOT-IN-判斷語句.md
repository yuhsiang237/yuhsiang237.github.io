---
title: 'PostgreSQL IN/NOT IN 判斷語句'
date: 2022-03-16 13:24:20
categories:
  - [Database,PostgreSQL]
---
在這主要介紹PostgreSQL IN、NOT IN的用法
IN:數值在這集合裡面。
NOT IN:數值在不再這集合裡面。

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
WHERE PR.PRICE IN ('100','200') 
```

結果:
```
id | price
---+------
1  | 100 
2  | 200
```
NOT IN:數值在不再這集合裡面。
```sql
SELECT * FROM PRICE_RECORD PR 
WHERE PR.PRICE NOT IN ('100','200') 
```

結果:
```
id | price
---+------
3  | 300
```





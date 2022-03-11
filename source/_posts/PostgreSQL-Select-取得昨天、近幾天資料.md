---
title: 'PostgreSQL Select 取得昨天、近幾天資料'
date: 2022-03-11 09:46:14
categories:
  - [Database,PostgreSQL]
---
### 前言
最近遇到在PostgreSQL取得近幾天資料的問題，於是在思考是否有比較直覺得寫法?
後來發現用Between或>=都可以。


以下有一個table:

**tb_record**
```
seq | record_date(character varying(8))
---- -----
1   | 20220101
2   | 20220103
3   | 20220305
4   | 20220308
5   | 20220310
```

比如今天是，20220310則:

取得昨天到今天的資料

between寫法:
```sql
SELECT 
    * 
FROM 
    TB_RECORD 
WHERE
    RECORD_DATE BETWEEN TO_CHAR(NOW() - INTERVAL '1 DAY','YYYYMMDD') AND TO_CHAR(NOW(),'YYYYMMDD')
```

大於等於寫法:
```sql
SELECT 
    * 
FROM 
    TB_RECORD 
WHERE
    RECORD_DATE >= TO_CHAR(NOW() - INTERVAL '1 DAY','YYYYMMDD')
```
結果:
```
5   | 20220310
```


取得近五天資料

between寫法:
```sql
SELECT 
    * 
FROM 
    TB_RECORD 
WHERE
    RECORD_DATE BETWEEN TO_CHAR(NOW() - INTERVAL '5 DAY','YYYYMMDD') AND TO_CHAR(NOW(),'YYYYMMDD')
```

大於等於寫法:
```sql
SELECT 
    * 
FROM 
    TB_RECORD 
WHERE
    RECORD_DATE >= TO_CHAR(NOW() - INTERVAL '5 DAY','YYYYMMDD')

```
結果:
```
3   | 20220305
4   | 20220308
5   | 20220310
```
### 總結
其實就是使用NOW()函數與INTERVAL條件去組日期區間去篩選(O)。
並且搭配一些內部的轉型。
以上面結果就是都轉成YYYYMMDD的字串去比較。
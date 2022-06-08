---
title: '[Day27] SQL server 數學函式 - SQL Server資料庫入門'
date: 2021-09-17 17:37:44
categories:
  - [Topic,2021 SQL Server Challenge]
  - [Database,SQL Server]
---
這回稍微帶一下可能會用到的數學函式。


### ROUND 四捨五入
```
SELECT ROUND(235.41500, 2) AS RoundValue;
```
結果:235.42000

### SQUARE 平方
```
select SQUARE(6);
```
結果:36
### SQRT 開根號
```
select SQRT(36);
```
結果:6

### FLOOR 地板
```
SELECT FLOOR(25.75) AS FloorValue; 
```
結果:25

### CEILING 天花板
```
SELECT CEILING(25.1) AS CeilValue; 
```
結果:26

### Abs 絕對值
```
SELECT Abs(-243.5) AS AbsNum; 
```
結果:243.5
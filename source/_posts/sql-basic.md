---
title: SQL基礎操作語法(select、insert、update、delete)
date: 2021-08-28 15:42:42
categories:
  - [SQL]
---


記得大學時常常寫SQL，所以語法很熟悉。
但先前工作是前端變成很少接觸這塊，幾乎快2年沒碰，現在就盡可能的把我學的記錄下來。
而更早之前則是使用ORM，之後就超級少遇到了，被ORM慣壞了。

這篇先從基礎開始。

查詢，對Table01裡的所有欄位進行搜尋
*表示所有欄位
```
select * from Table01
```

新增，對table插入一列值
```
insert into Table01 (name,email) values ('yuhsiang','test@test.com')
```

修改，對table的欄位去設定數值，並指定id為6的資料去修改
```
update Table01 set name='tom',email='tom@email.com' where id='6'
```
刪除，對指定的資料去刪除
```
delete Table01 where id='1'
```

可以發現，我們賦予值的資料都有''，而欄位則沒有。
且要修改特定某一筆時，是使用where加上該列的主鍵(PK)去辨識出該列的資料。

**參考資料：**
https://dotblogs.com.tw/jackbgova/2014/12/03/147522
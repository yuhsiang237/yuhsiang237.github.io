---
title: SQL Server 常見SQL語法筆記
date: 2022-01-05 07:54:08
categories:
  - [SQL]
---
### 前言
在這裡整理一些專案常用的SQL語法。

### 語法
1.新增一筆資料
```
insert into Todos (Name) Values ('123')
```
2.更新一筆資料
```
update Todos set Name = '4123',IsComplete = 1 where Name = '123'
```
3.刪除一筆資料
```
delete from Todos where id = 17
```
4.查詢該表所有資料
```
select * from Todos;
```
5.使用查詢結果新增資料
```
insert into Todos (Name) select Name from Todos where Name = '4123'
```
6.INNER JOIN 主表跟次要表都有相同的值才撈出
```
select * from ShipmentOrderDetails as a 
join Products as b on a.ProductId = b.Id
```
7.LEFT JOIN 主表值為主嘗試撈取次要表值，次要表有值就撈，沒值就NULL
```
select * from ShipmentOrderDetails as a 
left join Products as b on a.ProductId = b.Id
```
8.GROUP BY 取得以哪個欄位作為群，並且操作該群的集合資料
```
select OrderId,sum(ProductPrice * ProductUnit) sum from ShipmentOrderDetails 
group by OrderId
```
9.多對多資料處理(即使用left join/join的組合技)
```
select a.Id,a.Name,c.Id as ProductCategoryId,c.Name as ProductCategoryName from Products as a
left join ProductProductCategoryRelationships as b on a.Id = b.ProductId
left join ProductCategory as c on c.Id = b.ProductCategoryId
```
### 總結
這裡整理SQL常見用法。
因為在寫C#時其實內部會使用LINQ，所以就會對不常用的原生SQL生疏，因此在此整理在專案上常見的使用方法，有需要時就能節省再熟悉的時間。

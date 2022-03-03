---
title: '[Day26] C# MVC LINQ實作常見用法 - C#&AspNetCore'
date: 2021-12-27 19:20:46
categories:  
  - [Topic,2021 C# AspNetCore鐵人賽]
  - [Code,C#]
  - [Framework,AspNetCore MVC]
---
在這篇中主要紀錄一些常見的C# LINQ寫法，通常搭配資料庫EF Core使用。

1.簡單取得匹配某ID的"複數"筆資料列
```
var query = 
from a in _context.Roles
where a.Id == user.RoleId
select a;
```

2.取得某"單筆"物件資料

作法1:
```
var query = (from a in _context.Users
where a.Account == userAccount
select new
{
    Id = a.Id,
    Account = a.Account,
    RoleId = a.RoleId,
    Name = a.Name,
    Email = a.Email
}).FirstOrDefault()
```
作法2:
```
var query =  _context.Products.FirstOrDefault(x => x.Id == shipmentOrderDetail.ProductId);
```

差別在一個全是用.的方式寫。

3.群組資料結果。此例將日期變成群組，統計每日期具有的資料筆數。
且使用匿名class建立搜尋結果。
```
var query = from a in _context.ShipmentOrders
where a.IsDeleted != true
where a.DeliveryDate.Value.Month == DateTime.Now.Month
group a by a.DeliveryDate.Value.Date into g
select new { DeliveryDate = g.Key, Count = g.Count() }
```

4.主表a之後inner join b表的資料。
```
var query = from a in _context.ReturnShipmentOrders
join b in _context.ShipmentOrders
on a.ShipmentOrderId equals b.Id
where a.IsDeleted != true
select new ReturnShipmentOrderViewModel
{
Id = a.Id,
Number = a.Number,
ShipmentOrderNumber = b.Number,
Total = a.Total,
ReturnDate = a.ReturnDate
};
```

5.LINQ子查詢，Category表資料在另一個多對多表上，使用子查詢去取得結果。
```
var query = from a in _context.Products
where a.IsDeleted != true
select new ProductIndexViewModel
{
    Id = a.Id,
    Number = a.Number,
    Name = a.Name,
    CurrentUnit = a.CurrentUnit,
    Price = a.Price,
    Description = a.Description,
    Category = (from b in _context.ProductProductCategoryRelationships
                    join c in category
                    on b.ProductCategoryId equals c.Id
                    where b.ProductId == a.Id
                    select new ProductCategory
                    { 
                        Id = c.Id,
                        Name = c.Name
                    }).ToList()
};
```

6.取得總數統計，在此例以取得某年的Total加總。
```
var query = (from a in _context.ShipmentOrders
where a.IsDeleted != true
where a.FinishDate.Value.Year == DateTime.Now.Year
select a.Total).Sum()
```

7.取得查詢的結果數量
```
var query = (int)(from a in _context.Products
where a.IsDeleted != true
select a.Id).Count();
```

### 結論
主要在這篇中紀錄實作常見會寫到的作法。
此外，如果熟悉LINQ後反而會造成原生SQL語法淡忘，所以還是要加減注意一下原生作法。
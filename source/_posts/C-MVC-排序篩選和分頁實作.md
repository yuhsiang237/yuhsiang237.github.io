---
title: '[Day17] C# MVC 排序、篩選和分頁實作 - C#&MVC入門'
date: 2021-10-14 10:16:48
categories:
  - [程式語言,C#]
---
對於資料庫的操作我們介紹了兩種做法:
+ {% post_link 'C-MVC-Dapper用法' '[Day16] C# MVC Dapper用法與連結資料庫 - C#&MVC入門' %} 
+ {% post_link 'C-MVC-Model與資料庫連結' '[Day7] C# MVC Model模型連結資料庫，使用Entity Framework - C#&MVC入門'%}

而在上方兩篇文中我們都是撈頁面呈現單一資料。
因此這回來談談列表吧!

### 目標
以下是整體的目標:
+ 可搜尋過濾
+ 可排序
+ 可分頁

基本上有這三種功能，就是一個強大的列表了。
而剛好官方就有提供這方面的實作教學，因此就參考官方作法:
https://docs.microsoft.com/zh-tw/aspnet/core/data/ef-mvc/sort-filter-page?view=aspnetcore-3.1

### 實作
先附上完成後作品
{% asset_img "1.png" %}
實際運作樣子GIF
{% asset_img "3.gif" %}

目前整理程式碼中，預計明日放上。
基本上算是個很強的萬用Table了。


**參考資料**
https://docs.microsoft.com/zh-tw/aspnet/core/data/ef-mvc/sort-filter-page?view=aspnetcore-3.1
https://blog.csdn.net/mzl87/article/details/102924701
https://jscinin.medium.com/asp-net-core-mvc-part-12-%E7%82%BA%E9%99%B3%E5%88%97%E7%9A%84%E8%B3%87%E6%96%99%E8%A3%BD%E4%BD%9C%E5%88%86%E9%A0%81%E6%95%88%E6%9E%9C-%E5%A5%97%E4%BB%B6x-pagedlist-core-mvc-a2191d86317d
https://docs.microsoft.com/zh-tw/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
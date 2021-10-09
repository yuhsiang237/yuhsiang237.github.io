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


(未完 撰寫中

**參考資料**
https://docs.microsoft.com/zh-tw/aspnet/core/data/ef-mvc/sort-filter-page?view=aspnetcore-3.1
---
title: '[Day12] C# MVC 驗證與授權，登入與登出 - C#&MVC入門'
date: 2021-10-7 12:00:51
categories:
  - [程式語言,C#]
---
在上回 {% post_link 'C-MVC傳遞參數的方式' '[Day11] C# MVC傳遞參數的方式 - C#&MVC入門'%}  ，我們介紹了頁面與頁面間的參數傳遞。

而這回，將探討的是"權限"，不同權限的人看到的頁面也不同，是網頁中的一個必備功能。

對於權限.net core MVC本身就有提供一套可實作的方法，連結如下:
https://docs.microsoft.com/zh-tw/aspnet/core/security/authorization/introduction?view=aspnetcore-3.1

### 驗證與授權
步驟:驗證->授權
+ 驗證 (Authentication):讓系統認得你是誰
    + Cookie-Based驗證
    + Token-Based驗證
    + Idetity 驗證
    + OAuth2 驗證
    + Windows 驗證
+ 授權 (Authorization):讓系統判斷你是否有權限
    + AuthorizeAttribute授權

驗證有很多種沒有好壞之分，以我觀點來說就是:**「用官方範例並符合需求的簡單作法」**。
因為這樣能避免維護問題、要改動也容易些。
在這網頁上是.net core MVC，所以最簡單實作方式就是「Cookie-Based驗證」，未來要使用API時也只要增加「Token-Based驗證」即可。

### Cookie-Based 驗證
使用瀏覽器的 Cookie 儲存使用者驗證資訊。
要使用Cookie驗證要小心的是資料不能太多，因為瀏覽器有4096 bytes限制。
(如果是一些舊瀏覽器就會遇到問題，所以要注意資料就是存識別資料即可)


(未完...)

**參考資料**
https://docs.microsoft.com/zh-tw/aspnet/core/security/authorization/introduction?view=aspnetcore-3.1
https://ithelp.ithome.com.tw/articles/10249930
https://blogger.tigernaxo.com/post/dotnetcore31/auth/auth_guild_3/
https://ithelp.ithome.com.tw/articles/10199102
https://ithelp.ithome.com.tw/articles/10198150
https://blog.miniasp.com/post/2019/12/25/asp-net-core-3-cookie-based-authentication
https://dotblogs.com.tw/shadow/2019/01/16/105615
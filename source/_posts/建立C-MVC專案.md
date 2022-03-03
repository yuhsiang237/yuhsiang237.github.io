---
title:  '[Day3] 建立C# MVC專案 - C#&AspNetCore'
date: 2021-09-28 17:03:14
categories:  
  - [Topic,2021 C# AspNetCore鐵人賽]
  - [Code,C#]
---

在上回 {% post_link '什麼是MVC' '[Day2] 什麼是MVC，有什麼好處 - C#&AspNetCore'%} 中我們理解到的MVC的核心概念。
在這回中我們要利用Visual Studio來建立一個C# MVC專案，並運行起來。

### 安裝
Visual Studio Community 2019:
https://visualstudio.microsoft.com/zh-hant/downloads/

### 建立專案
1.下載完後就可以開啟Visual Studio建立專案，New>Project
{% asset_img "1.png" %}
2.選ASP.NET Core Web App(Model-View-Controller)，不然會建立出傳統沒有設計模式(Design Pattern)架構的專案。
{% asset_img "2.png" %}
3.設定專案名稱及資料夾
{% asset_img "3.png" %}
4.Enable Razor runtime compilation 執行期編譯要記得打勾，不然每次要重新執行才能看到修改的結果。
{% asset_img "4.png" %}
5.專案建立
{% asset_img "5.png" %}
6.可看見Model、View、Controller
{% asset_img "6.png" %}
7.點下Express執行
{% asset_img "7.png" %}
8.成功運行在網頁上了!
{% asset_img "8.png" %}


### 總結
建立MVC專案成功!
相對於PHP的LARAVEL框架來說，這套建立起來的MVC系統比較輕量，也就代表未來擴充的程式碼要設計好，否則量大起來加上多人協作可能就會有難以維護的問題。
為了在維護、co-work好改，之後也會注重在設計模式上。
最後，對於微軟程式只要能用IDE運行起來就成功一半了。因為程式碼除錯、追蹤功能強大。
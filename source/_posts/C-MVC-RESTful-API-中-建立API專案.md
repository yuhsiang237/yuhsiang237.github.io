---
title: '[Day20] C# MVC RESTful API (中) 建立API專案 - C#&MVC入門'
date: 2021-10-17 10:16:48
categories:
  - [程式語言,C#]
---
在上回中我們介紹了 {% post_link 'C-MVC-RESTful-API-上-RESTful-基礎知識' '[Day19] C# MVC RESTful API (上) RESTful 基礎知識 - C#&MVC入門' %} ，知道了RESTful API的基礎知識。

這回先了解如何在現有的MVC專案中加入API專案吧!

### MVC專案加入WEB API專案
1.這是一個原本的MVC專案
{% asset_img "1.png" %}
2.對Solution點右鍵>New Project
{% asset_img "2.png" %}
3.選ASP.NET Core Web API
{% asset_img "3.png" %}
4.建立API專案名稱，這取名TodoAPI
{% asset_img "4.png" %}
5.版本我選.net core 3.1長期穩定版本
{% asset_img "5.png" %}
6.這時就會有一個專案TodoAPI加入現有的Solution了。
{% asset_img "6.png" %}
7.切換運行的專案
{% asset_img "7.png" %}
8.執行，可出現預設的範例API結果
{% asset_img "8.png" %}

### 總結
在這篇中主要是探討如何在一個有MVC專案的狀況下再去加一個Web API服務。
而在下篇我們就要用這範例實作RESTful API。


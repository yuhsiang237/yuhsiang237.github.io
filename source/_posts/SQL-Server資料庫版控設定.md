---
title: 'SQL Server資料庫版控設定'
date: 2022-05-04 13:11:48
categories:
  - [Database,SQL Server]
---
### 前言
在寫程式時我們都會有版本控制，以避免重大失誤無法回版，而在程式上作法很簡單就是使用git。
但資料庫要怎版控呢?如果遇到有人直接把資料庫欄位異動了，卻沒通知其他人，程式就會變得難以除錯，所以在這回要介紹如何在資料庫加入版本控制功能。
在這篇範例會以SQL Server搭配Visual Studio去達成版控。

主要以微軟官方提供的:SQL Server Data Tools 來實作。

### 實作
1.因為要模擬真實情景，先以Visual Studio建立任一個沒版控的專案，在此先以ASP NET CORE MVC為範例。
{% asset_img "1.png" %}

2.對解決方案點右鍵，為這專案用的資料庫加入"資料庫版控專案"
{% asset_img "2.png" %}
3.選擇 SQL Server 資料庫專案，如果找不到這個專案記得去這裡裝:https://docs.microsoft.com/zh-tw/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-ver15
就可以出現這範例專案了。
{% asset_img "3.png" %}
4.我取名叫MyDatabase
{% asset_img "4.png" %}
5.這時就有一個MyDatabase的資料庫管理專案放進來了
{% asset_img "5.png" %}
6.再來我們現有的資料庫叫做TodoDB，預計把這DB加入版控
{% asset_img "6.png" %}
7.選擇匯入>資料庫
{% asset_img "7.png" %}
8.再來就連DB選擇指定資料庫
{% asset_img "8.png" %}
9.在此我就選擇資料庫連線、選擇指定資料庫
{% asset_img "9.png" %}
10.再來點選啟動
{% asset_img "10.png" %}
11.這樣就匯入完成
{% asset_img "11.png" %}
12.如此一來就可以版控了也可以設計資料庫
{% asset_img "12.png" %}
完成!
完整專案如下:
https://github.com/yuhsiang237/SQL-Server-Version-Control

### 參考資料
- https://blog.yowko.com/sql-server-version-control/
- https://visualstudio.microsoft.com/zh-hant/vs/features/ssdt/
- https://docs.microsoft.com/zh-tw/sql/ssdt/sql-server-data-tools?view=sql-server-ver15
- https://docs.microsoft.com/en-us/previous-versions/sql/sql-server-data-tools/hh864423(v=vs.103)
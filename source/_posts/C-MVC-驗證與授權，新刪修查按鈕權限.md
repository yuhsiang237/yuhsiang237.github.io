---
title: '[Day13] C# MVC 驗證與授權，新刪修查按鈕權限 - C#&MVC入門'
date: 2021-10-8 12:00:51
categories:
  - [程式語言,C#]
---
在上回 {% post_link 'C-MVC-驗證與授權登入與登出' '[Day12] C# MVC 驗證與授權，登入與登出 - C#&MVC入門'%}  ，我們介紹了登入與登出。

而這回，將探討的是**驗證與授權**的細部權限，即所謂的新刪修查權限。
如:某角色是禁止使用特定功能的編輯。

我在之前也有了打篇與這相關的資料表規劃 : {% post_link '角色權限資料表規劃' '資訊系統的角色權限規劃，含資料表設計'%}

### 授權
授權是指決定使用者能夠做什麼的處理常式。 例如，系統管理使用者可以建立文件庫、新增檔、編輯檔，以及將它們刪除。 使用媒體櫃的非系統管理使用者只會獲得讀取檔的授權。

### 實作
目標，可以讓一個有權限的使用者依照他的權限去限制:檢視、新增、刪除、編輯。

權限代碼:
+ Basic_UserManagement_View
+ Basic_UserManagement_Create
+ Basic_UserManagement_Modify
+ Basic_UserManagement_Delete

使用者擁有的權限代碼:
+ Basic_UserManagement_View
+ Basic_UserManagement_Create
+ Basic_UserManagement_Delete

所以該使用者只能檢視、建立、刪除，並且無法編輯。

在此先列出會檔案目錄，以及異動檔案(黃色標記處):
{% asset_img "1.png" %}


(未完。整理中)

**參考資料**
https://zh.wikipedia.org/wiki/%E4%BB%A5%E8%A7%92%E8%89%B2%E7%82%BA%E5%9F%BA%E7%A4%8E%E7%9A%84%E5%AD%98%E5%8F%96%E6%8E%A7%E5%88%B6
https://docs.microsoft.com/zh-tw/aspnet/core/security/authorization/views?view=aspnetcore-3.1
https://www.cnblogs.com/RainingNight/p/dynamic-authorization-in-asp-net-core.html
https://cloud.tencent.com/developer/article/1503626
---
title: '[Day7] C# MVC Model模型連結資料庫 - C#&MVC入門'
date: 2021-10-2 12:00:51
categories:
  - [程式語言,C#]
---
在上回 {% post_link 'C-MVC-Model模型驗證' '[Day6] C# MVC Model模型驗證 - C#&MVC入門'%}  ，我們學會利用Model建立驗證機制。
而這回將會介紹如何把Model與真實的資料庫連結再一起。

### Entity Framework 

Entity Framework (又稱ADO.NET Entity Framework) 是微軟以 ADO.NET 為基礎所發展出來的物件關聯對應 (O/R Mapping) 解決方案。
Entity Framework 利用了抽象化資料結構的方式，將每個資料庫物件都轉換成應用程式物件 (entity)，而資料欄位都轉換為屬性 (property)，關聯則轉換為結合屬性 (association)，讓資料庫的 E/R 模型完全的轉成物件模型，如此讓程式設計師能用最熟悉的程式語言來呼叫存取。

簡言之，就是一個透過物件的方式去操作資料，而不透過原生SQL的作法。
(但我還是比較熟悉原生的SQL，因為換什麼語言寫都通，但在微軟架構下還是用他提供的會比較好)

### 安裝Entity Framework
安裝:
+ Microsoft.EntityFrameworkCore
+ Microsoft.EntityFrameworkCore.SqlServer
+ Microsoft.EntityFrameworkCore.Design
+ Microsoft.EntityFrameworkCore.Tools

過程:
1.Dependencies> Manage nuget packages
{% asset_img "1.png" %}
2.安裝Microsoft.EntityFrameworkCore
{% asset_img "2.png" %}
3.安裝Microsoft.EntityFrameworkCore.SqlServer
{% asset_img "3.png" %}
4.安裝Microsoft.EntityFrameworkCore.Design
{% asset_img "4.png" %}
5.安裝Microsoft.EntityFrameworkCore.Tools
{% asset_img "5.png" %}
### 使用Entity Framework
Entity Framework Core 有提供兩種開發方式
+ DB First:我已經有現有資料庫了，只是用程式去操作他。
+ Code First:我還沒有資料庫，想用程式碼去建立資料庫。
+ Model First:先定義好模型在產生資料表的方式。

以我在Laravel的架構下寫過的經驗，會覺得Code First會比較好，因為有更動歷程，也比較能知道異動。
而DB First則是把兩者分開來做，優點是很直接，缺點就是異動後很難知道改了哪。

而這回主要以DB First為主。

1.開啟package manager console
{% asset_img "6.png" %}
2.之後會出現命令列
{% asset_img "7.png" %}
3.之後把SQL Server打開，看目前資料庫，我們預計把Orders資料庫給整合進來
{% asset_img "8.png" %}
4.使用<code>Scaffold-DbContext</code>指令，來還原Orders資料庫的Models
```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Orders' Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models

Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Orders;Trusted_Connection=True;MultipleActiveResultSets=true;user id=sa;password=sa" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models 

Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Orders;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer  -OutputDir Models 

```
請自行替換這些字串的內容:
+ Catalog:資料庫名稱
+ user id:資料庫管理員帳號
+ password:資料庫管理員密碼

如果出現以下狀況代表連線異常
```

未完，目前卡在連線無法連線，Scaffold-DbContext這段連不到資料庫。


**參考資料**
https://docs.microsoft.com/zh-tw/aspnet/core/data/ef-mvc/intro?view=aspnetcore-5.0
https://docs.microsoft.com/zh-tw/aspnet/core/tutorials/first-mvc-app/working-with-sql?view=aspnetcore-3.1&tabs=visual-studio
https://docs.microsoft.com/zh-tw/ef/core/cli/dotnet#dotnet-ef-dbcontext-scaffold
https://www.youtube.com/watch?v=2m_3SHGy-Rs
http://go.microsoft.com/fwlink/?LinkId=723263.
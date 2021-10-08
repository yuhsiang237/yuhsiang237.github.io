---
title:  '[Day16] C# MVC Dapper用法與連結資料庫 - C#&MVC入門'
date: 2021-10-12 11:44:45
categories:
  - [程式語言,C#]
---


在上回 {% post_link 'C-MVC-LINQ-常見用法' '[Day15] C# MVC LINQ常見用法 - C#&MVC入門'%}  ，我們介紹了LINQ常見用法。


發現LINQ在組合複雜的查詢比較困難，為了解決這困難通常會用原生寫法替代。
因此，這回我們用另一種方式連結資料庫，Dapper。

此外，如果是用Entity Framework core可以參考之前寫的這篇喔:{% post_link 'C-MVC-Model與資料庫連結' '[Day7] C# MVC Model模型連結資料庫 - C#&MVC入門'%}

### Dapper
Dapper是適用於Microsoft .NET平台的對象關係映射產品：它提供了將面向對象的域模型映射到傳統關係數據庫的框架。其目的是將開發人員從大部分與關係數據持久性相關的編程任務中解放出來。

### 安裝
1.用Nutget
{% asset_img "1.png" %}
2.搜尋Dapper安裝
{% asset_img "2.png" %}

### 實作
先列出異動的檔案目錄，黃色為我們會改的檔案:
{% asset_img "3.png" %}

1.首先就是要建立Dapper連結資料庫的類別

**~/Dapper/DataControl.cs**
```
using Dapper;
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Linq;
using System.Threading.Tasks;

namespace DapperExample.Dapper
{
    public class DataControl : IDisposable
    {
        private IDbConnection db = null;
        private string _connectStr;

        //建構子初始化連線字串/DB連接
        public DataControl()
        {
            // 自行替換資料庫連結字串
            string connectionStr = @"Server=.\SQLExpress;Database=Orders;Trusted_Connection=True;ConnectRetryCount=0";
            _connectStr = connectionStr;
            db = new SqlConnection(_connectStr);
        }

        //共用查詢方法
        public IEnumerable<T> Query<T>(string sql, object param)
        {
            return db.Query<T>(sql, param).ToList();
        }

        //共用新增刪除修改方法
        public int Execute(string sql, object param)
        {
            return db.Execute(sql, param);
        }

        //釋放連接
        public void Dispose()
        {
            db.Dispose();
        }
    }
}
```
說明:
唯一要改的在20行要套入自己的資料庫連結字串。

2.再來建立對應的資料表Model

**~/Models/Customer.cs**
```
using System.ComponentModel.DataAnnotations;
namespace DapperExample.Models
{
    public class Customer
    {
        [Display(Name = "編號")]
        public string Number { get; set; }
       
        [Display(Name = "名子")]
        [Required(ErrorMessage = "名字不可為空")]
        public string Name { get; set; }
       
        [Display(Name = "電話")]
        public string Tel { get; set; }
    }
}
```
說明:依照資料表去訂定自己的Model。

3.在Controller使用

**~/Controllers/HomeController.cs**
```
using DapperExample.Dapper;
using DapperExample.Models;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Threading.Tasks;

namespace DapperExample.Controllers
{
    public class HomeController : Controller
    {
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger)
        {
            _logger = logger;
        }

        public IActionResult Index()
        {
            Customer customer;
            // 使用using，區塊結束後會自動執行Dispose
            using (DataControl dc = new DataControl())
            {
                string number = "U001";
                string sql = @"SELECT * FROM Customers WHERE number = @number";
                var result = dc.Query<Customer>(sql, new { number });//執行DataControl的Query方法
                customer = result.First();
            }
            return View(customer);
        }

        public IActionResult Privacy()
        {
            return View();
        }

        [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
        public IActionResult Error()
        {
            return View(new ErrorViewModel { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
        }
    }
}

```
說明:
在26~32行使用using是Dapper的作用區域，離開後Dapper會自己Dispose。
注意:只有用using才會自動Dispose
我們把資料查出來後抓第一筆，設定到Customer Model，並傳給View。

4.呈現在畫面上
~/Views/Home/Index.cshtml
```
@model DapperExample.Models.Customer;
@{
    ViewData["Title"] = "Home Page";
}

<div class="text-center">
    <h1 class="display-4">Welcome</h1>
    <p>Learn about <a href="https://docs.microsoft.com/aspnet/core">building Web apps with ASP.NET Core</a>.</p>
</div>
<ul>
    <li>@Model.Number</li>
    <li>@Model.Name</li>
    <li>@Model.Tel</li>
</ul>
```
5.完成，網頁上結果如下:
{% asset_img "4.png" %}
資料庫資料:
{% asset_img "5.png" %}


### 結論
Dapper配置非常簡單!整體安裝到撰寫連線不用15分鐘。
更多的文件可以參考:https://dapper-tutorial.net/dapper
主要在複雜查詢、提升效能可以用，而普通的新/刪/修/查還是用Entity Framework core強型別會好一些。

如果是用Entity Framework core可以參考之前寫的這篇:{% post_link 'C-MVC-Model與資料庫連結' '[Day7] C# MVC Model模型連結資料庫 - C#&MVC入門'%}

**參考資料**
https://blog.darkthread.net/blog/dapper/
https://ithelp.ithome.com.tw/articles/10240341
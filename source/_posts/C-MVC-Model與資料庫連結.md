---
title: '[Day7] C# MVC Model模型連結資料庫，使用Entity Framework - C#&AspNetCore'
date: 2021-10-2 12:00:51
categories:  
  - [Topic,2021 C# AspNetCore Challenge]
  - [Code,C#]
  - [Framework,AspNetCore MVC]
---
在上回 {% post_link 'C-MVC-Model模型驗證' '[Day6] C# MVC Model模型驗證 - C#&AspNetCore'%}  ，我們學會利用Model建立驗證機制。
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
Scaffold-DbContext "Server=.\SQLExpress;Database=Orders;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```
請自行替換字串的內容:
+ Database:資料庫名稱

{% asset_img "9.png" %}
之後Models資料夾就會自動產生Model了
{% asset_img "10.png" %}

再來至Startup.cs添加資料庫連線服務
##### Startup.cs
```
      public void ConfigureServices(IServiceCollection services)
        {
            
            services.AddControllersWithViews();
            // 資料庫配置
            var connection = @"Server=.\SQLExpress;Database=Orders;Trusted_Connection=True;ConnectRetryCount=0";
            services.AddDbContext<OrdersContext>(options => options.UseSqlServer(connection));
        }
```
再來至控制器HomeController.cs增加方法
##### HomeController.cs
```
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Threading.Tasks;
using WebApplication1.Models;

namespace WebApplication1.Controllers
{
    public class HomeController : Controller
    {
        private readonly ILogger<HomeController> _logger;

        // 添加Context
        private readonly OrdersContext _context;


        public HomeController(ILogger<HomeController> logger, OrdersContext context)
        {
            _logger = logger;
            // 初始化Context
            _context = context;
        }

        [HttpGet]
        public IActionResult Create()
        {
            return View();
        }

        [HttpPost]
        public IActionResult Create(UsersModel usersmodel)
        {
            if (ModelState.IsValid)
            {
                //如果連結資料庫在這進行儲存

                //導回首頁
                return RedirectToAction(nameof(Index));
            }
            return View(usersmodel);
        }


        public IActionResult Index()
        {
            // 透過Context取得資料
            var model = _context.Customers.Select(b => new Customer
            {
                Name = b.Name,
                Number = b.Number,
                Tel = b.Tel
            }).ToList();

            // 取第一列資料出來
            Customer m = model[0];

            return View(m);
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
再來在View上修改
```
@model WebApplication1.Models.Customer;
@{
    ViewData["Title"] = "Home Page";
}
<ul>
    <li>@Model.Number</li>
    <li>@Model.Name</li>
    <li>@Model.Tel</li>
</ul>
<div class="text-center">
    <h1 class="display-4">Welcome</h1>
    <p>Learn about <a href="https://docs.microsoft.com/aspnet/core">building Web apps with ASP.NET Core</a>.</p>
</div>
```
接下來運行即可印出第一筆資料
{% asset_img "11.png" %}
{% asset_img "12.png" %}

### 總結
而現在已經能成功連結資料庫了!
微軟MVC資料庫連結方式比較麻煩，因為要自行找套件，不像Laravel已經配好，直接打帳號密碼就能用。
而現在迫切問題在Controller只能傳送一個Model，要如何讓多個Model能夠透過Controller傳送到View上。
所以，下個篇主要介紹的就是Controller。

**參考資料**
https://docs.microsoft.com/zh-tw/aspnet/core/data/ef-mvc/intro?view=aspnetcore-5.0
https://docs.microsoft.com/zh-tw/aspnet/core/tutorials/first-mvc-app/working-with-sql?view=aspnetcore-3.1&tabs=visual-studio
https://docs.microsoft.com/zh-tw/ef/core/cli/dotnet#dotnet-ef-dbcontext-scaffold
https://www.youtube.com/watch?v=2m_3SHGy-Rs
http://go.microsoft.com/fwlink/?LinkId=723263
https://www.uuu.com.tw/Public/content/article/20/20200413.htm
http://ikevin.tw/2019/08/04/asp-net-core-3-0-%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8-database-first/
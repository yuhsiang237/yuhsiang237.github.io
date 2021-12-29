---
title:  '[Day4] C# MVC的程式架構淺談 - C#&AspNetCore'
date: 2021-09-29 07:05:32
categories:
  - [程式語言,C#]
---
這篇主要檢討上回 {% post_link '建立C-MVC專案' '[Day3] 建立C# MVC專案 - C#&AspNetCore'%} 透過.net core 3.1版本建立出的程式碼架構。


### 檔案架構
+ wwwroot資料夾: 是放置靜態檔案如js、css等前台需要的檔案
+ Controllers資料夾: 是放置MVC的控制器Controller的地方，與使用者請求並給予回應有關
+ Models資料夾: 是放置MVC的控制器Models的地方，與資料庫有緊密關係
+ Views資料夾: 是放置MVC的控制器Views的地方，在頁面上和使用者互動的畫面
+ appsetting.json 設定檔案。
+ Program.cs 主要的進入點Main的所在，在此建立Host。
+ Startup.cs 在這裡可以設定網站的啟用設定，如添加中介層(Middleware) 

{% asset_img "1.png" %}

### 程式碼探究
#### Program.cs
```
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace WebApplication1
{
    public class Program
    {
        public static void Main(string[] args)
        {
            CreateHostBuilder(args).Build().Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
    }
}
```
+ Main:程式碼進入點，並在裡面呼叫<code>CreateHostBuilder</code>，建立初始化的<code>IHostBuilder</code>。之後，Build建立實體，再透過Run運行。
+ CreateDefaultBuilder:預設為空參數，也可以自行添加。
+ ConfigureWebHostDefaults:其中包含用來裝載 web 應用程式的預設值
+ UseStartup:指定要供 web 主機使用的啟動類型，這裡套用Startup.cs的程式碼，也可以自訂Class然後套用至此。

小結:透過一個Console程式運行網站伺服器，可在此設定一開始要執行什麼、套用什麼設定。

#### Startup.cs
```
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.HttpsPolicy;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace WebApplication1
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // 添加服務
            services.AddControllersWithViews();
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }
            app.UseHttpsRedirection();
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
        }
    }
}

```
+ Startup:建構式，程式進入點，裡面設定configuration(如:appsetting.json的資料就會在此被帶進來)。
+ ConfigureServices:設定添加的服務。<code>AddControllersWithViews</code>，主要用來設定添加MVC服務，舊版是AddMvc。
+ Configure:設定HTTP request pipeline，針對http請求進行設定。
    + IsDevelopmen:套用開發版/上線版兩種模式。
    + UseHsts:將 HTTP Strict 傳輸安全性通訊協定 (HSTS) 標頭傳送給用戶端
    + UseDeveloperExceptionPage:使用開發者例外頁面
    + UseExceptionHandler:例外時導向某頁
    + UseHttpsRedirection:強制導向HTTPS
    + UseStaticFiles:會在中呼叫方法 Startup.Configure，以啟用靜態檔案的服務
    + UseRouting:將路由對應新增到Middleware中
    + UseAuthorization:授權使用端點路由傳送的資源時，此呼叫必須出現在對應用程式的呼叫之間 。UseRouting () 和 應用程式。UseEndpoints ( ... ) ，讓中介軟體能夠正常運作。
    + UseEndpoints:Middleware提供了endpoints可以擴充一些方法去針對route去做設定

小結:添加哪些服務、設定一些初始架構(HTTP協定、開發版/上線版)、中介層設定都在這Startup.cs裡。

#### appsettings.json
```
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```
小結:在此建立的值可以在Startup.cs被讀取，通常是一些外部字串設定檔，像是舊版的webconfig

#### HomeController.cs
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

        public HomeController(ILogger<HomeController> logger)
        {
            _logger = logger;
        }

        public IActionResult Index()
        {
            return View();
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
+ ILogger:提供寫入log的介面，在 ASP.NET Core 統一透過 ILogger 來記錄 Log。
+ Index()、Privacy():是Controller裡的action，用來載入的View資料夾的頁面。
+ ResponseCache:設定 Response 的暫存方式。並套用到要使用 Response 快取的 Controller 或 Action。以此就是套用到Error()。
+ Error():錯誤時返回的頁面，這邊回傳了ErrorViewModel模型格式的內容。
[補充]C#問號用法:
```
int? x = null;//定義可空型別變數
int? y = x ?? 1000;//使用合併運算子，當變數x為null時，預設賦值1000
```

小結:控制器可依需求返回頁面、可在此紀錄LOG。

#### ErrorViewModel.cs
```
using System;

namespace WebApplication1.Models
{
    public class ErrorViewModel
    {
        public string RequestId { get; set; }

        public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);
    }
}
```
+ ErrorViewModel:建立Class
+ RequestId:model中的string值
+ ShowRequestId:model中的bool值，返回一個lambda函式結果

給View用的Model，跟MVVM概念中所提到的ViewModel是不一樣的
在MVC的View中，一個View Binding一個Model，如果要使用多個不同的資料表就會用到View Model。

小結:在此建立一些模型規格，供頁面呈現使用。

#### Index.cshtml
```
@{
    ViewData["Title"] = "Home Page";
}

<div class="text-center">
    <h1 class="display-4">Welcome</h1>
    <p>Learn about <a href="https://docs.microsoft.com/aspnet/core">building Web apps with ASP.NET Core</a>.</p>
</div>
```

+ ViewData:引用System.Web.Mvc.ViewDataDictionary，屬於Dictionary Object，能放Property或是一個Model的IEnumerable，使用Key/Value的概念存取。生命週期為一個頁面
+ @{}區塊:Razor語法，可以在裡面寫C#語法，但建議前台盡量少業務邏輯、只以純顯示為幕的比較好。

小結:在此可以寫html、前端畫面。


### Privacy.cshtml
```
@{
    ViewData["Title"] = "Privacy Policy";
}
<h1>@ViewData["Title"]</h1>

<p>Use this page to detail your site's privacy policy.</p>
```
+ @ViewData:可用Razor語法叫出值顯示。

小結:同 Index.cshtml

### _Layout.cshtml
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"] - WebApplication1</title>
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css" />
    <link rel="stylesheet" href="~/css/site.css" />
</head>
<body>
    <header>
        <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
            <div class="container">
                <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">WebApplication1</a>
                <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse" aria-controls="navbarSupportedContent"
                        aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
                </button>
                <div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
                    <ul class="navbar-nav flex-grow-1">
                        <li class="nav-item">
                            <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                        </li>
                    </ul>
                </div>
            </div>
        </nav>
    </header>
    <div class="container">
        <main role="main" class="pb-3">
            @RenderBody()
        </main>
    </div>

    <footer class="border-top footer text-muted">
        <div class="container">
            &copy; 2021 - WebApplication1 - <a asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
        </div>
    </footer>
    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
    <script src="~/js/site.js" asp-append-version="true"></script>
    @RenderSection("Scripts", required: false)
</body>
</html>

```
+ @ViewData["Title"]:底下頁面PASS過來的Title。
+ @RenderBody():把整塊子頁面放進來。
+ @RenderSection():建立放js區塊的程式碼區域，required表示是否一定要提供。

小結:共用的頁面主架構。
#### _ValidationScriptsPartial.cshtml
```
<script src="~/lib/jquery-validation/dist/jquery.validate.min.js"></script>
<script src="~/lib/jquery-validation-unobtrusive/jquery.validate.unobtrusive.min.js"></script>
```
小結:放切出來的區塊。
#### Error.cshtml
```
@model ErrorViewModel
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

@if (Model.ShowRequestId)
{
    <p>
        <strong>Request ID:</strong> <code>@Model.RequestId</code>
    </p>
}

<h3>Development Mode</h3>
<p>
    Swapping to <strong>Development</strong> environment will display more detailed information about the error that occurred.
</p>
<p>
    <strong>The Development environment shouldn't be enabled for deployed applications.</strong>
    It can result in displaying sensitive information from exceptions to end users.
    For local debugging, enable the <strong>Development</strong> environment by setting the <strong>ASPNETCORE_ENVIRONMENT</strong> environment variable to <strong>Development</strong>
    and restarting the app.
</p>
```
@model:Razor語法，可以使用model的物件
小結:套用了ErrorViewModel，主要印出ErrorViewModel內的資料。
#### _ViewImports.cshtml
```
@using WebApplication1
@using WebApplication1.Models
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```
小結:設定使用那些程式模組

#### _ViewStart.cshtml
```
@{
    Layout = "_Layout";
}
```
小結:頁面進入點套用。

### 總結

從程式碼片段中可以得知整個架構上如何運作。
從一開始的Console建置啟動程式、Startup設定，至單個頁面程式碼符合MVC架構的撰寫，最後運行起整個系統。

因為MVC所以知道這設計模式的人就能很快的熟悉這架構的寫法與邏輯。
如果是自幹的專案，就會有這地方是什麼架構、要怎麼改的才能達到功能的各種歪路。

預計下回會針對在Model、View、Controller上的細節程式碼撰寫。

**參考資料**
https://www.tutorialsteacher.com/core/aspnet-core-startup
https://blog.johnwu.cc/article/asp-net-core-3-application-lifetime.html
https://ithelp.ithome.com.tw/articles/10240077
https://docs.microsoft.com/zh-tw/dotnet/api/microsoft.extensions.hosting.host.createdefaultbuilder?view=dotnet-plat-ext-5.0
https://ithelp.ithome.com.tw/articles/10243332
https://ithelp.ithome.com.tw/articles/10186520
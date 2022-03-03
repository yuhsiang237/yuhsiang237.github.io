---
title: '[Day5] C# MVC Model模型使用 - C#&AspNetCore'
date: 2021-09-30 12:00:51
categories:  
  - [Topic,2021 C# AspNetCore鐵人賽]
  - [Code,C#]
  - [Framework,AspNetCore MVC]
---
在上回 {% post_link 'C-MVC的程式架構淺談' '[Day4] C# MVC的程式架構淺談 - C#&AspNetCore'%}  ，我們談了整體的架構與檔案。
在這篇主要談如何在 ASP.NET Core MVC 中建立Model模型，並且使用。

### Model定義
模型，是一個Class，在MVC裡是放資料的容器，通常會因為以下原因建立:
1. 為了對應資料庫的table schema(欄位名稱)
2. 呈現頁面資料的ViewModel

### 建立Model的過程
1.在Models資料夾 Add > New Item
{% asset_img "1.png" %}
2.選擇Class，然後自訂Model名稱
{% asset_img "2.png" %}
3.自己增加欄位ID、Name、Age、Address。
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace WebApplication1.Models
{
    public class UsersModel
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Age { get; set; }
    }
}
```
4.完成，建立一個Model就是這麼簡單，再來就是使用這個Model。

### 使用Model
再來我們透過HomeController使用他。

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
            // 建立usersModel物件
            UsersModel usersModel = new UsersModel()
            {
                ID = 1,
                Name = "YU HSIANG",
                Age = 25
            };
            // 傳入View
            return View(usersModel);
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
23~31，透過模型Model建立規格，再把資料指派進去，傳進View中。

接下來要在View中使用我們傳進的Model

##### Index.cshtml
```
@model WebApplication1.Models.UsersModel
@{
    ViewData["Title"] = "Home Page";
}
<ul>
    <li>@Model.ID</li>
    <li>@Model.Name</li>
    <li>@Model.Age</li>
</ul>
<div class="text-center">
    <h1 class="display-4">Welcome</h1>
    <p>Learn about <a href="https://docs.microsoft.com/aspnet/core">building Web apps with ASP.NET Core</a>.</p>
</div>
```
如此一來就能顯示我們的資料了。
{% asset_img "4.png" %}

此外，通常會建立ViewModel以對應特定的View畫面。

**[補充]View型態主要分為兩種**
Type View(強型別)-compiler時就必須過，有intellisense
Dynamic View(動態)-intellisense不會協助，在View具有不同class的model可以使用

### 總結
知道Model有ViewModel與table schema的用法，能夠使我們更清楚訂定需要的資料規格，讓維護或擴充時有一個良好的先天條件。@@
而在下篇中將會探討Model的資料驗證。

**參考資料**
https://ithelp.ithome.com.tw/articles/10240268
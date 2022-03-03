---
title: '[Day18] C# MVC專案中撰寫API - C#&AspNetCore'
date: 2021-10-15 10:16:48
categories:  
  - [Topic,2021 C# AspNetCore鐵人賽]
  - [Code,C#]
  - [Framework,AspNetCore MVC]
---
在上回中我們介紹了 {% post_link 'C-MVC-排序篩選和分頁實作' '[Day17] C# MVC 排序、篩選和分頁實作 - C#&AspNetCore' %} ，能呈現出列表了。

而這回就要講能讓事情比較省事的API。
### API
應用程式介面（英語：Application Programming Interface），縮寫為API，是一種計算介面，它定義多個軟體中介之間的互動，以及可以進行的呼叫（call）或請求（request）的種類，如何進行呼叫或發出請求，應使用的資料格式，應遵循的慣例等。

簡單來說就是:程式的接口，可以傳送參數給後端處理的函式，之後會把所需的資料回傳你。

### 實作API
這回先介紹任意命名的API。

1.在Controller撰寫API，第37~45行

**~/Controller/HomeController.cs**
```
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using MVC_With_API.Models;
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Threading.Tasks;

namespace MVC_With_API.Controllers
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

        [HttpPost]
        public JsonResult getSum(int num)
        {
            int sum = 0;
            for(int i = 0; i < num; i++)
            {
                sum += i;
            }
            return Json(new { result = sum });
        }
    }
}
```
說明:
如上第37~45行就是一個API了，外界可以用POST方式Request，然後經由裡面的計算去得到一個結果，並回傳Response。
這種隨意風格的API大概僅適合在特定Controller底下使用。

2.再來我們用測試工具PostMan查看結果
https://www.postman.com/downloads/
說明:我們用body傳送了參數num，並設定為10，會從1+2+3...+10=45得到45，可從Response接到值。
{% asset_img "1.png" %}

3.嘗試在頁面上呼叫

**~/Views/Home/Index.cshtml**

```
@{
    ViewData["Title"] = "Home Page";
}

<div class="text-center">
    <h1 class="display-4">Welcome</h1>
    <p>Learn about <a href="https://docs.microsoft.com/aspnet/core">building Web apps with ASP.NET Core</a>.</p>
</div>

<div id="result"></div>

@section Scripts{
    <script type="text/javascript">
    $.post("Home/getSum",
        {
            num: "10",
        },
        function (data, status) {
            $('#result').html(data.result)
        });
    </script>
}

```
說明:第95~105行就是呼叫的AJAX了!
並把結果印到html上。

結果:
{% asset_img "2.png" %}
### 總結

這種簡易無規範的API比較適合只在Controller底下寫。
如果要開給外界比較推薦用RESTful API。

至於為什麼RESTful呢?
因為各種getPeopleData、updatePeople、getPeopleList...會造成維護上不好掌控，沒有固定格式，不好直覺知道結果。
但如果專案內部有風格統一API，且偏向自用，那其實也不需要RESTful。

下回會嘗試使用RESTful API的方式撰寫。

然後相較於之前Laravel開發的經驗，感覺.net core MVC切路由方式不太直覺@@。

**參考資料**
https://docs.microsoft.com/zh-tw/aspnet/core/web-api/?view=aspnetcore-3.1
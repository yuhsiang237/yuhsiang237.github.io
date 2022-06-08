---
title: '[Day9] C# MVC View使用 - C#&AspNetCore'
date: 2021-10-4 12:00:51
categories:  
  - [Topic,2021 C# AspNetCore Challenge]
  - [Code,C#]
  - [Framework,AspNetCore MVC]
---
在上回 {% post_link 'C-MVC-Controller控制器使用' '[Day8] C# MVC Controller控制器使用 - C#&AspNetCore'%}  ，我們介紹了Controller的基本用法。

而這回就是使用者畫面了，也就是MVC的View

在這篇中會介紹:
1. View的Razor語法
2. 可共用的View 部分檢視Partial View

### View的Razor語法
通常在View中看到@出現通常就是Razor語法了。
基本上就是印出值，再來就是一些特定用法。

+ 後端註解
```
@*註解*@
```
+ 輸出@符號
```
@@
```
+ @變數名稱，取值
```
@DateTime.Now
```
+ 陳述式
```
@(5*3)
```
+ 程式碼區塊
```
@{ }
```
+ for用法
```
@for (int i = 0; i < 10; i++)
{
  <text>
    word   
  </text>
}
```
+ foreach用法
```
@{
    string[] fruits = { "apple", "orange", "banana" };    
    <ul>
      @foreach (var item in fruits)
      {
        <li>@item</li>    
      }
    </ul>
}
```
+ 變數
```
@{
    var name = "mvc";  
}  
<p>@name 您好</p>
```




### Partial View 部分檢視
在網頁時常會有需要**共用的畫面**，如側邊欄、頂端橫幅等。
不想要在每個頁面都寫一個，這時就會使用到Partial View把他們切出去。

Partial View前面會有個_底線
可以看到預設建立的MVC專案就有不少Partial View了。
{% asset_img "1.png" %}


##### _ViewStart.cshtml
```
@{
    Layout = "_Layout";
}
```
Layout的進入點_ViewStart.cshtml在此可以選擇預設要套用的版，如一些網站會有前台、後台之分。
如果想把View切成前後台可以參考這篇動態切換適配的Layout:
https://dotblogs.com.tw/stanley14/2016/06/29/202231
其實就是在_ViewStart.cshtml這檔案做些適配而已


再來我們看_Layout.cs，版面前台主View

##### _Layout.cs
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
+ <code>@RenderSection("Scripts", required: false)</code>用來輸出主板中區段的內容。required是指此Section是否一定要有資料顯示，如果設成true時，檢視中沒有定義此Section的值，那網頁就會掛掉。
  子版則可以將下列區段內容輸出到主板
  ```
  @section Scripts {
      <script type="text/javascript" src="~/scripts/main.js"></script>
  }
  ```
+ <code>@RenderBody()</code>所有被導向主版的view會被載入到@RenderBody

### 切Partial的方法
我們可以發現_Layout.cs有段nav區塊可以共用，把他切出去吧。

##### _Nav.cs
```
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
```
之後在_Layout將他引進

##### _Layout.cs
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
        @await Html.PartialAsync("_Nav")
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
+ <code>@await Html.PartialAsync("_Nav")</code> 表示用異步的方式把Partial區塊引進來。使用 HTML 協助程式時，最佳做法是使用 PartialAsync。 PartialAsync 會傳回包裝在 Task<TResult> 的 IHtmlContent 類型。 方法的參考方式，是在等候的呼叫前面加上 @ 字元。此外，目前以不建議使用同步方式載入了，因為官方說會造成死結。

如此以來就會更專注在每個小區塊。

這時可能有人會問，Partial沒Controller怎麼載資料呢?所以要介紹另個載入的方式。

### Partial傳遞資料的方式
我們查看<code>@await Html.PartialAsync("_Nav")</code>的函式多載，可以發現能透過Model方式將資料傳入。
{% asset_img "2.png" %}

我們建立一個ViewModel叫NavViewModel
##### NavViewModel.cs
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace WebApplication1.Models
{
    public class NavViewModel
    {
        public string BrandName { get; set; }
    }
}
```
將原本的PartialAsync傳入NavViewModel
##### _Layout.cshtml
```
@await Html.PartialAsync("_Nav",new NavViewModel() { BrandName = "WebApplication1" })
```
之後在_Nav.cshtml顯示
##### _Nav.cshtml
```
@model WebApplication1.Models.NavViewModel;
<nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
    <div class="container">
        <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">
            @Model.BrandName
        </a>
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
```
+ <code>@Model.BrandName</code>即可印出Model資料

因為會拉出去獨立寫的通常都是固定規格，所以建議用Model，而不是用弱型別的ViewData、ViewBag方式。



### 總結
MVC的View真的做到很嚴謹，讓整體資料有固定的方式作呈現，關鍵就在那一個View只能綁一個Model，讓資料減少亂竄的可能性。
而在Partial也是可以透過API方式去自行載入資料。

預計下篇可能還是View，會探討一些常見作法在拆頁面結構上。

**參考資料**
https://ithelp.ithome.com.tw/articles/10160185
https://sites.google.com/site/yuedumvcbiji/4-view/layout-cshtml
https://docs.microsoft.com/zh-tw/aspnet/core/mvc/views/layout?view=aspnetcore-5.0
https://docs.microsoft.com/zh-tw/aspnet/core/mvc/views/partial?view=aspnetcore-5.0
https://ithelp.ithome.com.tw/articles/10186517
https://ithelp.ithome.com.tw/articles/10205881
https://ithelp.ithome.com.tw/articles/10240368
https://dotblogs.com.tw/stanley14/2016/06/29/202231
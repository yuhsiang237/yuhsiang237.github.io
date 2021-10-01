---
title: '[Day8] C# MVC Controller控制器使用 - C#&MVC入門'
date: 2021-10-3 12:00:51
categories:
  - [程式語言,C#]
---
在上回 {% post_link 'C-MVC-Model與資料庫連結' '[Day7] C# MVC Model模型連結資料庫 - C#&MVC入門'%}  ，我們把Model連上了資料庫，算是把Model的基礎流程跑完了。

這回就來看Controller吧!

在這篇中主要介紹Controller把資訊傳送給View，有以下方法
1. ViewData
2. ViewBag
3. 多個Model傳給View


### 控制器（Controller）
負責轉發請求，對請求進行處理。
當使用者進到了前台的視圖（View），對畫面操作所觸發的事件就會跑到控制器。
在控制器裡負責拿到模型（Model）的資料後加上些業務邏輯後返視圖(View)。
負責處理主要商業邏輯處理，資料格式處理，大部分介於Model 與 View 之間，處理資料流的溝通。

### 建立Controller
1.在Controller資料夾Add > Controller
{% asset_img "1.png" %}
2.選Empty
{% asset_img "2.png" %}
3.MVC Controller Empty
{% asset_img "3.png" %}
4.接下來會產生程式碼，我們增加一行ViewData的程式碼
##### TestController.cs
```
using Microsoft.AspNetCore.Mvc;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace WebApplication1.Controllers
{
    public class TestController : Controller
    {
        public IActionResult Index()
        {
            // 使用ViewData
            ViewData["Message"] = "Hello World!";
            return View();
        }
    }
}

```
+ IActionResult:表示動作結果，即使用Post([HttpPost])或Get( [HttpGet])時會使用。
+ ViewData:表示在該View可以使用的Data，為弱型別。通常我們會多建立一個ViewModel作為強型別用，因為可以在編譯時就知道錯誤，上線後、維護也比較穩。ViewData算是有點偷懶的傳資料方式。

接下來我們添加View畫面，Test>Index.cshtml

{% asset_img "4.png" %}
##### Index.cshtml
```
@*
    For more information on enabling MVC for empty projects, visit https://go.microsoft.com/fwlink/?LinkID=397860
*@
@ViewData["Message"]

```
如此一來就能把資料帶進來了
{% asset_img "5.png" %}

### 補充Result種類
JsonResult:用來單純回傳JSON
```
public JsonResult TestJsonResult()
{
    return Json(new { data = 5 });
}
```
結果:
{% asset_img "6.png" %}

FileResult:用來回傳檔案
```
public FileResult TestFileResult()
{
    string excelPath = @"C:\example.xlsx";
    return File(excelPath, "application/vnd.ms-excel", "example.xlsx");
}
```
### Controller傳多個Model進View的方法
最好作法就是強型別的ViewModel了。

我們建立一個Model，裡面宣告Model類別屬性

##### TestViewModel.cs
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace WebApplication1.Models
{
    public class TestViewModel
    {
        Customer Customer { get; set; }
        public List<Product> Products { get; set; }

    }
}

```

##### TestController.cs
```
using Microsoft.AspNetCore.Mvc;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using WebApplication1.Models;

namespace WebApplication1.Controllers
{
    public class TestController : Controller
    {
        public IActionResult Index()
        {
            // 使用ViewData
            ViewData["Message"] = "Hello World!";

            TestViewModel testViewModel = new TestViewModel();
            testViewModel.Customer = new Customer() ;
            testViewModel.Customer.Name = "YU HSIANG";
            testViewModel.Customer.Number = "A001";
            testViewModel.Customer.Tel = "00-00000000";

            testViewModel.Products = new List<Product>();
            testViewModel.Products.Add(new Product() { Name = "PS4", Number = "P001", Price = 11000 });
            testViewModel.Products.Add(new Product() { Name = "PS5", Number = "P002", Price = 15000 });

            return View(testViewModel);
        }
        public JsonResult TestJsonResult()
        {
            return Json(new { data = 5 });
        }


        public FileResult TestFileResult()
        {
            string excelPath = @"C:\example.xlsx";
            return File(excelPath, "application/vnd.ms-excel", "example.xlsx");
        }
    }
}
```

##### Index.cshtml
```
@model WebApplication1.Models.TestViewModel
@*
    For more information on enabling MVC for empty projects, visit https://go.microsoft.com/fwlink/?LinkID=397860
*@
@ViewData["Message"]<br>
@Model.Customer.Name<br>
@Model.Customer.Number<br>
@Model.Customer.Tel<br>

<table>
@foreach (Product item in Model.Products)
{
    <tr>
        <td>@Html.TextBoxFor(m => item.Number)</td>
        <td>@Html.TextBoxFor(m => item.Name)</td>
        <td>@Html.TextBoxFor(m => item.Price)</td>
    </tr>
}
</table>
```
成功呈現多個Model，並且又是強型別
{% asset_img "7.png" %}

### 額外的方式傳值Viewbag

```
public IActionResult Index()
{
    // 使用ViewData
    ViewData["Message"] = "Hello World!";

    //使用ViewBag
    ViewBag.msg = "使用ViewBag的資料";

    TestViewModel testViewModel = new TestViewModel();
    testViewModel.Customer = new Customer() ;
    testViewModel.Customer.Name = "YU HSIANG";
    testViewModel.Customer.Number = "A001";
    testViewModel.Customer.Tel = "00-00000000";

    testViewModel.Products = new List<Product>();
    testViewModel.Products.Add(new Product() { Name = "PS4", Number = "P001", Price = 11000 });
    testViewModel.Products.Add(new Product() { Name = "PS5", Number = "P002", Price = 15000 });


    return View(testViewModel);
}
```
##### Index.cshtml
```
@model WebApplication1.Models.TestViewModel
@*
    For more information on enabling MVC for empty projects, visit https://go.microsoft.com/fwlink/?LinkID=397860
*@
@ViewData["Message"]<br>
@ViewBag.msg<br>
@Model.Customer.Name
<br>
@Model.Customer.Number
<br>
@Model.Customer.Tel
<br>

<table>
    @foreach (Product item in Model.Products)
    {
        <tr>
            <td>@Html.TextBoxFor(m => item.Number)</td>
            <td>@Html.TextBoxFor(m => item.Name)</td>
            <td>@Html.TextBoxFor(m => item.Price)</td>
        </tr>
    }
</table>

```
結果:
{% asset_img "8.png" %}
用ViewBag跟ViewData缺點都在弱型別，難以在編譯時就知道錯誤存在。
### 總結
從這篇中可以知道如何透過Controller傳資料給View。
在下篇預計就是介紹View了。

**參考資料**
https://ithelp.ithome.com.tw/articles/10240345
https://docs.microsoft.com/zh-tw/aspnet/core/web-api/action-return-types?view=aspnetcore-5.0
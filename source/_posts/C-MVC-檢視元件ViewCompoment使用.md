---
title: '[Day10] C# MVC 檢視元件View Compoment使用- C#&AspNetCore'
date: 2021-10-05 12:06:42
categories:  
  - [Topic,2021 C# AspNetCore Challenge]
  - [Code,C#]
  - [Framework,AspNetCore MVC]
---
在上回 {% post_link 'C-MVC-View使用' '[Day9] C# MVC View使用 - C#&AspNetCore' %} ，我們介紹了View的操作及用法，並稍微帶了Partial View。
而這回，我們將介紹檢視元件View Compoment。

因為原本MVC5的<code>@HTML.action</code>在.net core MVC中已經被移除掉了，造成Partial View會無法綁後端Controller撰寫商業邏輯。
因此，要改用View Compoment的做法。

### View Compoment
檢視元件與部分檢視類似，但功能更強大。 檢視元件不會使用模型繫結(看你要不要都可以)，並且只取決於呼叫它時所提供的資料。 

檢視元件：

+ 轉譯區塊，而不是整個回應。
+ 包含控制器與檢視之間的相同關注點分離和可測試性優點。
+ 可以有參數和商務邏輯。
+ 它通常是從配置頁面叫用。
+ 不參與控制器生命週期，無法使用Filter。
+ 非 HTTP 端點的呼叫，不處理Http Request/Response ，ViewComponent是透過程式碼叫用 。 

簡言之，可以撰寫商務邏輯。
有一個專門負責這View Component商務邏輯的程式碼。而你在View中也能把參數傳進View Component裡面。

### View Compoment建立與使用
照微軟官方文件，View Compoment有限定的資料夾結構，避免維護困難。
黃色標記是等等會新增的檔案。
{% asset_img "1.png" %}

+ <code>ViewComponents資料夾</code>必須在根目錄下新增這資料夾名稱才可以使用ViewComponents，否則底下的程式碼會出現錯誤。
  + <code>ShowUserInfo.cs</code>是以View Component為名稱命名的業務邏輯管理器。
+ <code>Shared/Components資料夾</code>底下都是以**資料夾**為名的Component名稱。
    + <code>ShowUserInfo資料夾</code>為View Component名稱，底下有一個Default.cshtml，為Component的視圖。

1.首先先建立業務邏輯的檔案

**ShowUserInfo.cs**
```
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using WebApplication1.Models;

namespace WebApplication1.ViewComponents
{
    public class ShowUserInfo : ViewComponent
    {
        public async Task<IViewComponentResult> InvokeAsync(string  number, string name,string tel)
        {
            Customer customer = new Customer();
            customer.Name = name;
            customer.Number = number;
            customer.Tel = tel;
            return View(customer);
        }
    }
}
```
+ <code>IViewComponentResult</code>這段回應照寫就好，算是固定寫法。

在13~19行可以用Model、ViewBag、ViewData，簡單來說就是當你在寫Controller裡的action那樣。
我這邊為了求謹慎，所以回傳的是個強型別Model，因為固定規格比較好維護。

2.再來是View Component的視圖

**ShowUserInfo.cshtml**

```
@model WebApplication1.Models.Customer;

<ul>
    <li>Number:@Model.Number</li>
    <li>Name:@Model.Name</li>
    <li>Tel:@Model.Tel</li>
</ul>

```
在此就把傳過來的資料顯示即可。

3.實際把View Component套用到View上。

**Index.cshtml**
```
@model WebApplication1.Models.Customer;
@using WebApplication1.ViewComponents;
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

// 呼叫View Component
@await Component.InvokeAsync(nameof(ShowUserInfo), new { number = "A007", name = "YS",tel="00-00000000" })

```
+ <code>@await Component.InvokeAsync</code>用這方法傳遞參數來呼叫View Component顯示。

如此一來就完成了，在網頁上呈現。

{% asset_img "2.png" %}

### 總結
整體流程大概是這樣:
1. 使用者進到View頁面
2. Controller接受並回應資料給View
3. View上的Razor語法呼叫ViewComponent
4. 把參數再丟給ViewComponent處理
5. 之後結果再回傳給整個View。

感覺這種方式真是爽快，功能又強，減少了很多Controller要顧慮的生命週期。
有獨立區塊用View Component會比用Partial View輕鬆很多。(時代的眼淚 誤~*


**參考資料**
https://dotblogs.com.tw/shadow/2021/04/10/174425
https://docs.microsoft.com/en-us/aspnet/core/mvc/views/view-components?view=aspnetcore-5.0
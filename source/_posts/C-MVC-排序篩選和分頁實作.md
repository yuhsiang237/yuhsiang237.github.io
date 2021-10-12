---
title: '[Day17] C# MVC 排序、篩選和分頁實作 - C#&MVC入門'
date: 2021-10-14 10:16:48
categories:
  - [程式語言,C#]
---
對於資料庫的操作我們介紹了兩種做法:
+ {% post_link 'C-MVC-Dapper用法' '[Day16] C# MVC Dapper用法與連結資料庫 - C#&MVC入門' %} 
+ {% post_link 'C-MVC-Model與資料庫連結' '[Day7] C# MVC Model模型連結資料庫，使用Entity Framework - C#&MVC入門'%}

而在上方兩篇文中我們都是撈頁面呈現單一資料。
因此這回來談談列表吧!

### 目標
以下是整體的目標:
+ 可搜尋過濾
+ 可排序
+ 可分頁

基本上有這三種功能，就是一個強大的列表了。
而剛好官方就有提供這方面的實作教學，因此就參考官方作法:
https://docs.microsoft.com/zh-tw/aspnet/core/data/ef-mvc/sort-filter-page?view=aspnetcore-3.1

### 實作
在此範例使用Entity Framework  Core作為示範。

先附上完成後結果:
{% asset_img "1.png" %}

修改檔案:
+ ~/Commons/PaginatedList.cs:分頁列表類別檔案，協助我們製作分頁。
+ ~/Controllers/OrderController.cs:訂單列表的控制器，負責整張列表的邏輯，如排序、分頁、搜尋。
+ ~/Models/ViewModels/OrderIndexViewModel.cs:用來呈現列表規格的ViewModel。
+ ~/Views/Order/Index.cshtml:列表畫面程式碼。

{% asset_img "4.png" %}

1.首先先建立列表分頁的類別

**~/Commons/PaginatedList.cs**
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.EntityFrameworkCore;

namespace ListExample
{
    public class PaginatedList<T> : List<T>
    {
        public int PageIndex { get; private set; }
        public int TotalPages { get; private set; }

        public PaginatedList(List<T> items, int count, int pageIndex, int pageSize)
        {
            PageIndex = pageIndex;
            TotalPages = (int)Math.Ceiling(count / (double)pageSize);

            this.AddRange(items);
        }

        public bool HasPreviousPage
        {
            get
            {
                return (PageIndex > 1);
            }
        }

        public bool HasNextPage
        {
            get
            {
                return (PageIndex < TotalPages);
            }
        }

        public static async Task<PaginatedList<T>> CreateAsync(IQueryable<T> source, int pageIndex, int pageSize)
        {
            var count = await source.CountAsync();
            var items = await source.Skip((pageIndex - 1) * pageSize).Take(pageSize).ToListAsync();
            return new PaginatedList<T>(items, count, pageIndex, pageSize);
        }
    }
}
```
說明:這是官方提供的類別，不需要動。
+ CreateAsync:主要讓我們把資料以PaginatedList的方式傳給View。
+ HasPreviousPage:判斷上一頁面是否存在，用來啟動跟關閉上一頁按鈕。
+ HasNextPage:判斷下一頁面，用來啟動跟關閉下一頁按鈕。

2.再來是資料規格，因為我們的資料有使用到兩張資料表(顧客、訂單)，因此會需要用到ViewModel。

**~/Models/ViewModels/OrderIndexViewModel.cs**
```
using System;
using System.ComponentModel.DataAnnotations;
namespace ListExample.Models.ViewModels
{
    public class OrderIndexViewModel
    {
        [Display(Name = "訂單編號")]

        public string Number { get; set; }
        [Display(Name = "寄送日期")]

        public DateTime ShippingDate { get; set; }
        [Display(Name = "寄送地址")]

        public string ShippingAddress { get; set; }
        [Display(Name = "客戶簽收")]

        public string CustomerSignature { get; set; }
        [Display(Name = "客戶編號")]

        public string CustomerNumber { get; set; }
        [Display(Name = "總額")]

        public decimal Total { get; set; }
        [Display(Name = "客戶名稱")]

        public string CustomerName { get; set; }
        [Display(Name = "客戶電話")]

        public string CustomerTel { get; set; }

    }
}
```

3.之後是Controller，負責我們分頁、查詢、排序的主要商業邏輯。
**~/Controllers/OrderController.cs**
```
using ListExample.Models;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using System;
using System.Threading.Tasks;
using System.Linq;
using ListExample.Models.ViewModels;

namespace ListExample.Controllers
{
    public class OrderController : Controller
    {

        private readonly OrdersContext _context;

        public OrderController(OrdersContext context)
        {
            _context = context;
        }
        public async Task<IActionResult> Index(
      string sortOrder,
      string currentFilterCustomer,
      string currentFilterNumber,
      string searchStringCustomer,
      string searchStringNumber,
      int? goToPageNumber,
      int pageSize,
      int? pageNumber)
        {
            // 1.搜尋邏輯
            var query = from a in _context.Orders
                        join b in _context.Customers on a.CustomerNumber equals b.Number
                        into result1
                        from ab in result1.DefaultIfEmpty()
                        select new OrderIndexViewModel
                        {
                            Number = a.Number,
                            ShippingAddress = a.ShippingAddress,
                            ShippingDate = a.ShippingDate,
                            CustomerSignature = a.CustomerSignature,
                            Total = a.Total,
                            CustomerNumber = a.CustomerNumber,
                            CustomerName = ab.Name,
                            CustomerTel = ab.Tel
                        };

            // 2.條件過濾
            if (searchStringCustomer != null || searchStringNumber != null)
            {
                pageNumber = 1;
            }
            else
            {
                searchStringCustomer = currentFilterCustomer;
                searchStringNumber = currentFilterNumber;
            }

            ViewData["CurrentFilterCustomer"] = searchStringCustomer;
            ViewData["CurrentFilterNumber"] = searchStringNumber;

            if (!String.IsNullOrEmpty(searchStringCustomer))
            {
                query = query.Where(s => s.CustomerName.Contains(searchStringCustomer));
            }
            if (!String.IsNullOrEmpty(searchStringNumber))
            {
                query = query.Where(s => s.Number.Contains(searchStringNumber));
            }

            // 3.排序依據
            ViewData["CurrentSort"] = sortOrder;

            switch (sortOrder)
            {
                case "1":
                    query = query.OrderByDescending(s => s.ShippingDate);
                    break;
                case "2":
                    query = query.OrderBy(s => s.ShippingDate);
                    break;
                case "3":
                    query = query.OrderByDescending(s => s.Total);
                    break;
                case "4":
                    query = query.OrderBy(s => s.Total);
                    break;
                default:
                    query = query.OrderByDescending(s => s.ShippingDate);
                    break;
            }

            // 4.前往頁數
            if (goToPageNumber != null)
            {
                pageNumber = goToPageNumber;
            }

            // 5.每頁筆數
            if (pageSize == 0)
            {
                pageSize = 10;
            }
            ViewData["pageSize"] = pageSize;
            
            // 6.返回結果
            return View(await PaginatedList<OrderIndexViewModel>.CreateAsync(query.AsNoTracking(), pageNumber ?? 1, pageSize));
        }

    }
}

```
說明:這範例也是由官方改造而成。
+ Index的參數:
  + sortOrder:排序的參數。
  + currentFilterCustomer:過濾Customer(客戶名稱)搜尋框的參數，暫存需要。
  + currentFilterNumber:過濾Number(訂單編號)搜尋框的參數，暫存需要。
  + searchStringCustomer:過濾Customer(客戶名稱)搜尋框的參數。
  + searchStringNumber:過濾Number(訂單編號)搜尋框的參數。
  + goToPageNumber:跳到某頁的參數。
  + pageSize:每頁筆數。
  + pageNumber:目前在第幾頁面。

主要流程為:建立查詢>過濾>排序>分頁。

+ 第31～45行:建立LINQ搜尋。
+ 第47～68行:用LINQ過濾，因為可能會有很多搜尋參數，在這範例中就有兩個，但要增加N個改法都一樣。算是:KISS (Keep It Simple, Stupid)。
+ 第71～90行:用LINQ排序。
+ 第92～103行:每頁筆數限制、跳至指定頁。
+ 第105行:回傳結果。

而使用ViewData是因為一些字串還需要留在頁面上，如:分頁、搜尋字串、排序。

4.前台畫面呈現

**~/Views/Order/Index.cshtml**

```
@model PaginatedList<ListExample.Models.ViewModels.OrderIndexViewModel>
@*
    For more information on enabling MVC for empty projects, visit https://go.microsoft.com/fwlink/?LinkID=397860
*@
@{
}
<h1>Order</h1>

<form asp-action="Index" class="mt-3" id="form_search">
    <div class="row">
        <div class="col-sm col-md-6 col-lg-3 col-xl-3">
            <label class="custom-label">客戶名稱</label>
            <div class="form-group form-row">
                <div class="col">
                    <input id="input_customer" type="text" class="form-control mr-2" name="searchStringCustomer" value="@ViewData["CurrentFilterCustomer"]" />
                </div>
            </div>
        </div>
        <div class="col-sm col-md-6 col-lg-3 col-xl-3">
            <label class="custom-label">訂單編號</label>
            <div class="form-group form-row">
                <div class="col">
                    <input id="input_number" type="text" class="form-control mr-2" name="searchStringNumber" value="@ViewData["CurrentFilterNumber"]" />
                </div>
            </div>
        </div>
    </div>
    <div class="text-right">
        <button type="submit" class="btn btn-secondary mb-2">查詢資料</button>
        <button class="btn btn-outline-secondary mb-2" onclick="clearSearch();">清空查詢</button>
    </div>
    <hr class="mt-0">
    <div class="d-flex justify-content-end">
        <div class="col-2 px-0">
            <div class="form-group">
                <select class="form-control" name="sortOrder" onchange="this.form.submit()">
                    <option value="0" selected="@((string)ViewData["CurrentSort"] == "0")">預設排序</option>
                    <option value="1" selected="@((string)ViewData["CurrentSort"] == "1")">出貨日期 高→低</option>
                    <option value="2" selected="@((string)ViewData["CurrentSort"] == "2")">出貨日期 低→高</option>
                    <option value="3" selected="@((string)ViewData["CurrentSort"] == "3")">訂單總額 高→低</option>
                    <option value="4" selected="@((string)ViewData["CurrentSort"] == "4")">訂單總額 低→高</option>
                </select>
            </div>
        </div>
    </div>


    <div class="table-responsive">

        <table class="table table-bordered">
            <thead>
                <tr>
                    <th>
                        訂單編號
                    </th>
                    <th>
                        出貨日期
                    </th>
                    <th>
                        寄送地址
                    </th>
                    <th>
                        客戶編號
                    </th>
                    <th>
                        客戶名稱
                    </th>
                    <th>
                        客戶電話
                    </th>
                    <th>
                        客戶簽收人
                    </th>
                    <th>
                        訂單總額
                    </th>
                </tr>
            </thead>
            <tbody>
                @foreach (var item in Model)
                {
                    <tr>
                        <td>
                            @Html.DisplayFor(modelItem => item.Number)
                        </td>

                        <td>
                            @Convert.ToDateTime(item.ShippingDate).ToString("yyyy-MM-dd")
                        </td>
                        <td>
                            @Html.DisplayFor(modelItem => item.ShippingAddress)
                        </td>
                        <td>
                            @Html.DisplayFor(modelItem => item.CustomerNumber)
                        </td>
                        <td>
                            @Html.DisplayFor(modelItem => item.CustomerName)
                        </td>
                        <td>
                            @Html.DisplayFor(modelItem => item.CustomerTel)
                        </td>
                        <td>
                            @if (item.CustomerSignature != "" && item.CustomerSignature != null)
                            {
                                @Html.DisplayFor(modelItem => item.CustomerSignature)
                            }
                            else
                            {
                                <span>-</span>
                            }
                        </td>
                        <td>
                            @Html.DisplayFor(modelItem => item.Total)
                        </td>
                    </tr>
                }
            </tbody>
        </table>
    </div>
    @{
        var prevDisabled = !Model.HasPreviousPage ? "disabled" : "";
        var nextDisabled = !Model.HasNextPage ? "disabled" : "";
    }

    <div class="list-pagination mt-3">
        <div class="form-inline text-center">
            <div class="mx-auto">
                每頁
                <select class="custom-select" name="pageSize" onchange="changePageSize()">
                    <option value="5" selected="@((int)ViewData["pageSize"]==5)">5</option>
                    <option value="10" selected="@((int)ViewData["pageSize"]==10)">10</option>
                    <option value="30" selected="@((int)ViewData["pageSize"]==30)">30</option>
                    <option value="50" selected="@((int)ViewData["pageSize"]==50)">50</option>
                </select>，第 <span>@(Model.TotalPages < Model.PageIndex ? 0 : Model.PageIndex) / @Model.TotalPages</span> 頁，共 <span>@Model.TotalPages</span> 頁，
                <a asp-action="Index"
                    asp-route-sortOrder="@ViewData["CurrentSort"]"
                    asp-route-pageNumber="@(Model.PageIndex - 1)"
                    asp-route-currentFilterNumber="@ViewData["CurrentFilterNumber"]"
                    asp-route-CurrentFilterCustomer="@ViewData["CurrentFilterCustomer"]"
                    asp-route-pageSize="@ViewData["PageSize"]"
                    class="btn btn-outline-secondary btn-sm @prevDisabled">
                    上一頁
                </a>｜跳至第
                <select id="select_goToPage" class="custom-select" name="goToPageNumber" onchange="goToPage();">
                    <option>
                        選擇
                    </option>
                    @for (var i = 1; i <= Model.TotalPages; i++)
                    {
                        <option value="@i" selected="@(Model.PageIndex == i)">
                            @i
                        </option>
                    }
                </select>
                頁｜
                <a asp-action="Index"
                   asp-route-sortOrder="@ViewData["CurrentSort"]"
                   asp-route-pageNumber="@(Model.PageIndex + 1)"
                   asp-route-currentFilterNumber="@ViewData["CurrentFilterNumber"]"
                   asp-route-CurrentFilterCustomer="@ViewData["CurrentFilterCustomer"]"
                   asp-route-pageSize="@ViewData["PageSize"]"
                   class="btn btn-outline-secondary btn-sm @nextDisabled">
                    下一頁
                </a>

            </div>
        </div>
    </div>
</form>


@section Scripts{
    <script type="text/javascript">
        function clearSearch() {
            $('#input_customer').val('')
            $('#input_number').val('')
            $('#form_search').submit()
        }
        function goToPage() {
            $('#form_search').submit()
        }
        function changePageSize() {
            $('#form_search').submit()
        }
    </script>
}
```
關鍵在把整個表都包進form裡面，共用一個Action。
第10～45行:為查詢、排序
第50～119行:為列表
第125～168行:為分頁區塊
第173～184行:讓一些操作可以再改變時自動執行form的Action。

5.完成後執行畫面:
{% asset_img "3.gif" %}


### 總結
這種要貼程式碼的很難去解釋，只能說照著做就能弄出一個列表。
官方 https://docs.microsoft.com/zh-tw/aspnet/core/data/ef-mvc/sort-filter-page?view=aspnetcore-3.1 也是這樣提供。
我跟官方作法差異在於:排序可以排多個、過濾可以過濾多個、有分頁前往的方式。
總之就是大方向是對的:建立查詢>過濾>排序>分頁。細節再修正成自己需求。

然後，其實用API套個前端列表會比較簡單，撈出全部然後交給前端表格元件。
但後端這種則比較穩定一些，因為編譯時就能知道錯誤，需要比較多程式碼才能達到。

最後，讓我比較訝異的是竟然找不到現成的非官方範例可以參考...。

完整程式碼:https://github.com/yuhsiang237/ASP.NET-Core-MVC-PaginatedList

**參考資料**
https://docs.microsoft.com/zh-tw/aspnet/core/data/ef-mvc/sort-filter-page?view=aspnetcore-3.1
https://blog.csdn.net/mzl87/article/details/102924701
https://jscinin.medium.com/asp-net-core-mvc-part-12-%E7%82%BA%E9%99%B3%E5%88%97%E7%9A%84%E8%B3%87%E6%96%99%E8%A3%BD%E4%BD%9C%E5%88%86%E9%A0%81%E6%95%88%E6%9E%9C-%E5%A5%97%E4%BB%B6x-pagedlist-core-mvc-a2191d86317d
https://docs.microsoft.com/zh-tw/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
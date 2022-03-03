---
title: '[Day6] C# MVC Model模型驗證 - C#&AspNetCore'
date: 2021-10-1 12:00:51
categories:  
  - [Topic,2021 C# AspNetCore鐵人賽]
  - [Code,C#]
  - [Framework,AspNetCore MVC]
---
在上回 {% post_link 'C-MVC-Model模型使用' '[Day5] C# MVC Model模型使用 - C#&AspNetCore'%}  ，我們建立了基本的Model，並且示範讓資料藉由Model資料呈現在網頁上。
在這篇主要談如何Model模型上進行驗證。

### Model的驗證
為了讓資料進到資料庫是符合我們要的格式，就必須在前面先擋過一層，而這層我們可以透過Model來達到。

**常見的驗證屬性**
+ [ValidateNever]： ValidateNeverAttribute 指出屬性或參數應該從驗證中排除。
+ [CreditCard]：驗證屬性是否具有信用卡格式。 需要 JQuery 驗證的其他方法。
+ [Compare]：驗證模型中的兩個屬性是否相符。
+ [EmailAddress]：驗證屬性具有電子郵件格式。
+ [Phone]：驗證屬性具有電話號碼格式。
+ [Range]：驗證屬性值是否落在指定的範圍內。像是最大值(Max)、最小值(min)
+ [RegularExpression]：驗證屬性值是否符合指定的正則運算式。
+ [Required]：驗證欄位不是 null。 
+ [StringLength]：驗證字串屬性值不超過指定的長度限制。
+ [Url]：驗證屬性是否具有 URL 格式。
+ [Remote]：在伺服器上呼叫動作方法，以驗證用戶端上的輸入。
+ [Display]:顯示名稱為
+ [DataType]:資料型別

### 添加驗證屬性範例實作
我們先在UsersModel.cs增加屬性
需先添加<code>using System.ComponentModel.DataAnnotations</code>才可使用驗證屬性。

##### UsersModel.cs

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using System.ComponentModel.DataAnnotations;

namespace WebApplication1.Models
{
    public class UsersModel
    {
        [Display(Name = "編號")]
        public int ID { get; set; }

        [Required(ErrorMessage = "姓名不可為空")]
        [Display(Name = "姓名")]
        public string Name { get; set; }

        [Display(Name = "年齡")]
        public int Age { get; set; }
    }
}
```
接著在HomeController.cs裡面個添加一個HttpGet、HttpPost的Create方法
HttpGet是進頁面時會跑的方法
HttpPost是表單觸發發送後會跑的方法
##### HomeController.cs
```
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
```

接著新增一個View頁面，在前台畫面上製作一個form表單。
##### Create.cshtml
```
@model WebApplication1.Models.UsersModel
@{
    ViewData["Title"] = "Create Page";
}
<form asp-action="Create">
    <div>
        <label asp-for="Name"></label>
        <input asp-for="Name" />
        <span asp-validation-for="Name"></span>
    </div>
    <div>
        <label asp-for="Age"></label>
        <input asp-for="Age" />
        <span asp-validation-for="Age"></span>
    </div>
    <div>
        <input type="submit" value="Save" />
    </div>
</form>
```

注意，<code>asp-validation-for="Name"</code>表示後端回傳的錯誤訊息會傳送到這裡。


然後至網址:https://localhost:44378/Home/Create 查看後
名子不填儲存就會出現錯誤了
{% asset_img "1.gif" %}
在程式碼上會發現跑到HttpPost的Create裡面
{% asset_img "2.gif" %}

### 自訂驗證
系統預設的驗證肯定不夠，所以就來製作自己的驗證。

我們在UsersModel.cs裡面增加一個CheckValidAge的class並且繼承ValidationAttribute
並覆寫裡面IsValid方法。

##### UsersModel.cs
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using System.ComponentModel.DataAnnotations;

namespace WebApplication1.Models
{
    public class UsersModel
    {
        [Display(Name = "編號")]
        public int ID { get; set; }

        [Required(ErrorMessage = "姓名不可為空")]
        [Display(Name = "姓名")]
        public string Name { get; set; }

        [CheckValidAge]
        [Display(Name = "年齡")]
        public int Age { get; set; }
    }

    public class CheckValidAge : ValidationAttribute
    {

        public CheckValidAge()
        {
            ErrorMessage = "年齡不可超過50歲";
        }

        public override bool IsValid(object value)
        {
            if (value == null)
            {
                return true;
            }
            int age = (int)value;
            if (age > 50)
            {
                return false;
            }
            else
            {
                return true;
            }
        }
    }
}

```
如此一來，送出如果>50就會顯示出錯誤。
{% asset_img "3.gif" %}


### 總結
雖然2021年的今天，大多數人都不使用這種form驗證，而是用API傳送。
因為頁面不用跳轉，用戶也能得到比較好的體驗。

然而，這種傳統的送資料方式還是有存在必要，就是系統追求安全性、穩定性，因為API方式會有比較難管理的問題。

此外，寫到這裡就開始有綁手綁腳的感覺
因為框架不那麼自由的讓你直接寫想寫的程式碼，而是照MVC架構
這就是一個好處，代表維護或後續開發的人就會照同條路走

**參考資料**
https://ithelp.ithome.com.tw/articles/10240340
https://docs.microsoft.com/zh-tw/aspnet/core/mvc/models/validation?view=aspnetcore-5.0



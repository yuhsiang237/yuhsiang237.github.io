---
title: '[Day28] C# MVC FluentValidation 好用的驗證格式套件 - C#&AspNetCore'
date: 2021-12-27 23:19:59
tags:
---
這回介紹一個必用的驗證神器FluentValidation，在處理驗證時會比原本Model驗證好上很多。因為他能適應不同的條件變化。

首先先看一張DEMO圖，就是類似表單驗證什麼都可以使用。
{% asset_img "rundebug2.gif" %}
## 安裝
1.開啟NuGet管理畫面
{% asset_img "1.png" %}
2.因為我是.net core專案所以安裝FluentValidation.AspNetCore，如果不是就裝第一個
{% asset_img "2.png" %}

## 撰寫一個簡單驗證
在此以帳密登入為範例

1.先建立一個Model
**Models/LoginViewModel.cs**
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace FluentValidationExample.Models
{
    public class LoginViewModel
    {
        public string Account { get; set; }
        public string Password { get; set; }
    }
}
```
2.撰寫該Model驗證
**Models/Validator/LoginViewModel.cs**
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using FluentValidation;

namespace FluentValidationExample.Models.Validator
{
    public class LoginValidator : AbstractValidator<LoginViewModel>
    {
        public LoginValidator()
        {
            RuleFor(x => x.Account).NotNull().WithMessage("名稱不可為空");
            RuleFor(x => x).Custom((x, c) =>
            {
                if (x.Password == "" || x.Password == null)
                {
                    c.AddFailure("Password", "密碼不可為空");
                }
            });
        }

    }
}
```
最主要是繼承抽象的AbstractValidator，而裡面傳進去的多型是LoginViewModel，即要驗證的Model。
再來就是用RuleFor去驗證。
我寫了一個內建、一個客製的。
此外，如果有連結資料庫也能從外面把Context傳進來。

3.再來撰寫登入的Post事件
**Controllers/HomeController.cs**
```
[HttpPost]
public IActionResult Index(LoginViewModel model)
{
    LoginValidator validator = new LoginValidator();
    ValidationResult result = validator.Validate(model);
    if (!result.IsValid)
    {
        foreach (ValidationFailure failer in result.Errors)
        {
            ModelState.AddModelError(failer.PropertyName, failer.ErrorMessage);
        }
    }
    return View();
}
```
基本上就是套用剛剛完成的validator。
當!result.IsValid時將錯誤訊息塞給ModelState，在前台就能顯示。

4.前台畫面cshtml撰寫。
```
@model FluentValidationExample.Models.LoginViewModel;

<div class="text-center">
    <h1 class="display-4">Welcome</h1>
    <p>Learn about <a href="https://docs.microsoft.com/aspnet/core">building Web apps with ASP.NET Core</a>.</p>
</div>
<div asp-validation-summary="ModelOnly"></div>

<form asp-action="Index">
    Account <input asp-for="Account" /> <span asp-validation-for="Account"></span>
    <br />
    Password <input asp-for="Password" /> <span asp-validation-for="Password"></span>
    <br />
    <input type="submit" value="submtit" />
</form>
```
主要就塞入asp-validation-for，他會與ModelState匹配，剛剛增加的錯誤訊息就能收到並顯示在畫面上了。

6.完成，然後嘗試用Debug跑一次
{% asset_img "rundebug.gif" %}


### 總結
為什麼不用Model驗證呢?
因為不同情況如某些時候欄位必填，有時不是，這時就會有問題。
而FluentValidation則完整破除這問題，並且能以邏輯方式條列撰寫，在釐清數據正確與否非常有幫助。
因此建議還再使用Model驗證的人可以快點換成FluentValidation，複雜的條件也能迎刃而解。

在此附上專案連結:
https://github.com/yuhsiang237/FluentValidation-Project

更多範例可以參考官方:
https://docs.fluentvalidation.net/en/latest/aspnet.html
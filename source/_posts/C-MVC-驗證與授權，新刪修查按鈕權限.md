---
title: '[Day13] C# MVC 驗證與授權，新刪修查按鈕權限 - C#&MVC入門'
date: 2021-10-8 12:00:51
categories:
  - [程式語言,C#]
---
在上回 {% post_link 'C-MVC-驗證與授權登入與登出' '[Day12] C# MVC 驗證與授權，登入與登出 - C#&MVC入門'%}  ，我們介紹了登入與登出。

而這回，將探討的是**驗證與授權**的細部權限，即所謂的新刪修查權限。
如:某角色是禁止使用特定功能的編輯。

我在之前也有了打篇與這相關的資料表規劃 : {% post_link '角色權限資料表規劃' '資訊系統的角色權限規劃，含資料表設計'%}

### 授權
授權是指決定使用者能夠做什麼的處理常式。 例如，系統管理使用者可以建立文件庫、新增檔、編輯檔，以及將它們刪除。 

### 實作
目標，可以讓一個有權限的使用者依照他的權限去限制:檢視、新增、刪除、編輯。

權限代碼:
+ Basic_UserManagement_View
+ Basic_UserManagement_Create
+ Basic_UserManagement_Modify
+ Basic_UserManagement_Delete

使用者擁有的權限代碼:
+ Basic_UserManagement_View
+ Basic_UserManagement_Create
+ Basic_UserManagement_Delete

所以該使用者只能檢視、建立、刪除，並且無法編輯。

在此先列出檔案目錄，以及異動檔案(黃色標記處):
{% asset_img "1.png" %}

1.首先先建立權限代碼的定義檔案
**~/Authorization/Permissions.cs**
```
namespace CookieAuthentication.Authorization
{ 
    // 權限代碼
    public static class Permissions
    {
        // 基本資料管理-人員管理
        public const string Basic_UserManagement_View = "Basic_UserManagement_View";
        public const string Basic_UserManagement_Create= "Basic_UserManagement_Create";
        public const string Basic_UserManagement_Modify = "Basic_UserManagement_Modify";
        public const string Basic_UserManagement_Delete = "Basic_UserManagement_Delete";
    }
}
```
說明:
未來如果要新增各種功能代碼都可以從這新增。

2.建立權限的AuthorizationRequirement，要求規格
**~/Authorization/PermissionAuthorizationRequirement.cs**
```
using Microsoft.AspNetCore.Authorization;
namespace CookieAuthentication.Authorization
{
    public class PermissionAuthorizationRequirement : IAuthorizationRequirement
    {
        public string[] Permissions { get; set; }

        public PermissionAuthorizationRequirement(string[] permissions)
        {
            Permissions = permissions;
        }
    }
}
```
說明:
為了要能夠自訂權限的邏輯，所以需要一個IAuthorizationRequirement幫我們傳遞授權資料的格式。


3.建立權限邏輯的控制器

**~/Authorization/PermissionAuthorizationHandler.cs**
```
using Microsoft.AspNetCore.Authorization;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace CookieAuthentication.Authorization
{
    // 權限控制邏輯
    public class PermissionAuthorizationHandler : AuthorizationHandler<PermissionAuthorizationRequirement>
    {
        protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, PermissionAuthorizationRequirement requirement)
        {
            // 如果沒有登入直接返回失敗
            var accountClaim = context.User.FindFirst(x => x.Type == "Account");
            if (accountClaim == null)
            {
                context.Fail();
                return Task.CompletedTask;
            }

            // 要求的權限
            var requirementPermissions = requirement.Permissions;

            // 取得使用者的權限， 這段可以從資料庫撈，在這範例用陣列
            List<string> userPermission = new List<string>();
            userPermission.Add(Permissions.Basic_UserManagement_View);
            userPermission.Add(Permissions.Basic_UserManagement_Delete);
            userPermission.Add(Permissions.Basic_UserManagement_Modify);

            // 檢查是否使用者具備權限    
            bool isExist = false;

            for (int i = 0; i < userPermission.Count(); i++)
            {
                for (int j = 0; j < requirementPermissions.Count(); j++)
                {
                    if (userPermission[i] == requirementPermissions[j])
                    {
                        isExist = true;
                        break;
                    }
                }
            }
            // 如果具備則授予成功
            if (isExist)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```
說明:
在此繼承了AuthorizationHandler，是為了要撰寫自己的判斷邏輯。
主要在HandleRequirementAsync裡面檢查傳進來的權限與使用者是否匹配，如果是就設定context.Succeed(requirement)；不是就設定context.Fail()。並在最後回傳return Task.CompletedTask。
在22～48是關鍵，在這如果連結資料庫就從資料庫抓使用者的權限表來比對即可。
我在24～28先用假資料替代資料庫撈出來的情況。

4.建立過濾器Filter，之後要用來套用在Controller的Action上

**~/Authorization/PermissionFilter.cs**
```
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Filters;
using Microsoft.Extensions.DependencyInjection;
using System;
using System.Threading.Tasks;

namespace CookieAuthentication.Authorization
{
    public class PermissionFilter:Attribute, IAsyncAuthorizationFilter
    {
        public string[] permissions { get; set; }

        public PermissionFilter(params string[] permissions)
        {
            this.permissions = permissions;
        }

        public async Task OnAuthorizationAsync(AuthorizationFilterContext context)
        {
            var authorizationService = context.HttpContext.RequestServices.GetRequiredService<IAuthorizationService>();
            var authorizationResult = await authorizationService.AuthorizeAsync(context.HttpContext.User, null, new PermissionAuthorizationRequirement(permissions));
            if (!authorizationResult.Succeeded)
            {
                // 如果授權失敗，設定為未授權
                context.Result = new UnauthorizedResult();
            }
        }
    }
}
```
說明:
重點在21～27，使用authorizationService.AuthorizeAsync去認證權限，就會把資料傳到我們上面建立的PermissionAuthorizationHandler.cs裡面判斷。
如果最後判斷沒權限則設定context.Result = new UnauthorizedResult();表示禁止。

5.增加過濾器服務Startup.cs

**~/Startup.cs**
```
using CookieAuthentication.Authorization;
using Microsoft.AspNetCore.Authentication.Cookies;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using System;
namespace CookieAuthentication
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
            // 註冊需求和處理常式，套用自訂權限控制器
            services.AddSingleton<IAuthorizationHandler, PermissionAuthorizationHandler>();

            // 從appsettings.json讀取登入逾時設定
            double LoginExpireMinute = this.Configuration.GetValue<double>("LoginExpireMinute");

            // 建立驗證中介軟體服務
            services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme).AddCookie(option =>
            {
                // 登入逾期設定，如果沒給預設14天
                option.ExpireTimeSpan = TimeSpan.FromMinutes(LoginExpireMinute);
                // 限制cookie不能延期
                option.SlidingExpiration = false;
            });

            services.AddControllersWithViews(options => {
                //CSRF資安有關，這裡就加入全域驗證範圍Filter的話，待會Controller就不必再加上[AutoValidateAntiforgeryToken]屬性
                options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute());
            });
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

            app.UseAuthentication(); // 啟用身份驗證
            app.UseAuthorization(); // 啟用授權，指的是Controller、Action可加上驗證 [Authorize] 屬性


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
說明
只有在26行，增加一行，表示套用驗證邏輯的PermissionAuthorizationHandler
```
// 註冊需求和處理常式，套用自訂權限控制器
services.AddSingleton<IAuthorizationHandler, PermissionAuthorizationHandler>();
```
6.限制頁面上的檢視權限，在Controller增加權限過濾器Filter

**~/Controllers/HomeController.cs**
```
[PermissionFilter(Permissions.Basic_UserManagement_View)]
public IActionResult Privacy()
{
    StringBuilder sb = new StringBuilder();
    sb.AppendLine("<ul>");

    foreach (Claim claim in HttpContext.User.Claims)
    {
        sb.AppendLine($@"<li> claim.Type:{claim.Type} , claim.Value:{ claim.Value}</li>");
    }
    sb.AppendLine("</ul>");

    ViewBag.msg = sb.ToString();
    return View();
}
```
說明:
添加
[PermissionFilter(Permissions.Basic_UserManagement_View)]
表示允許某權限的代碼進入，在此就是Permissions.Basic_UserManagement_View，表示畫面的檢視權限。

7.限制頁面上的按鈕權限

**~/View/Home/Privacy.cshtml**
```
@using Microsoft.AspNetCore.Authorization
@using CookieAuthentication.Authorization

@inject IAuthorizationService AuthorizationService
@{
    ViewData["Title"] = "Privacy Policy";
}
<h1>@ViewData["Title"]</h1>

<p>Use this page to detail your site's privacy policy.</p>

<!-- 按鈕區塊 -->
@if ((await AuthorizationService.AuthorizeAsync(User, null, new PermissionAuthorizationRequirement(new string[] { Permissions.Basic_UserManagement_Create }))).Succeeded)
{
    <button type="submit">新增</button>
}

@if ((await AuthorizationService.AuthorizeAsync(User, null, new PermissionAuthorizationRequirement(new string[] { Permissions.Basic_UserManagement_Modify }))).Succeeded)
{
    <button type="submit">編輯</button>
}

@if ((await AuthorizationService.AuthorizeAsync(User, null, new PermissionAuthorizationRequirement(new string[] { Permissions.Basic_UserManagement_Delete }))).Succeeded)
{
    <button type="submit">刪除</button>
}
<!-- ./按鈕區塊 -->

<!--登出區塊-->
<div>
    您的登入資訊↓
</div>
<div>
    @Html.Raw(ViewBag.msg)
</div>
<div>
    <a href="@Url.Action("Logout","Home")">登出</a>
</div>
<!--./登出區塊-->
```
說明:
在13~27為限制擁有某些權限的代碼才啟用
舉例:
@if ((await AuthorizationService.AuthorizeAsync(User, null, new PermissionAuthorizationRequirement(new string[] { Permissions.Basic_UserManagement_Modify }))).Succeeded)



8.完成!
該使用者登入後只有:畫面檢視、刪除、新增權限，而沒有編輯權限。
可以見到成功進Privacy頁面，而只有新增、刪除按鈕有顯示，編輯按鈕則因為權限限制沒有被顯示出來。
{% asset_img "2.gif" %}

### 總結
如此一來權限問題也難不倒了@@
而接下來主要以資料呈現為主題，所以下回是LINQ的資料操作。

**參考資料**
https://docs.microsoft.com/zh-tw/aspnet/core/security/authorization/views?view=aspnetcore-3.1
https://docs.microsoft.com/zh-tw/aspnet/core/security/authorization/resourcebased?view=aspnetcore-3.1


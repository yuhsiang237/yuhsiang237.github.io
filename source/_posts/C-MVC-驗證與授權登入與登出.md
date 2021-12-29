---
title: '[Day12] C# MVC 驗證與授權，登入與登出 - C#&AspNetCore'
date: 2021-10-7 12:00:51
categories:
  - [程式語言,C#]
---
在上回 {% post_link 'C-MVC傳遞參數的方式' '[Day11] C# MVC傳遞參數的方式 - C#&AspNetCore'%}  ，我們介紹了頁面與頁面間的參數傳遞。

而這回，將探討的是**驗證與授權**，並以實作登入、登出為示範。

此外，對於權限.net core MVC本身就有提供可實作的方法，連結如下:
https://docs.microsoft.com/zh-tw/aspnet/core/security/authorization/introduction?view=aspnetcore-3.1

### 驗證與授權
步驟:驗證->授權
+ 驗證 (Authentication):讓系統認得你是誰
    + Cookie-Based驗證
    + Token-Based驗證
    + Idetity 驗證
    + OAuth2 驗證
    + Windows 驗證
+ 授權 (Authorization):讓系統判斷你是否有權限
    + AuthorizeAttribute授權

驗證有很多種沒有好壞之分，以我觀點來說就是:**「用官方範例並符合需求的簡單作法」**。
因為這樣能避免維護問題、要改動也容易些。
在這網頁上是.net core MVC，所以最簡單實作方式就是「Cookie-Based驗證」，未來要使用API時也只要增加「Token-Based驗證」即可。

### Cookie-Based 驗證
使用瀏覽器的 Cookie 儲存使用者驗證資訊。
要使用Cookie驗證要小心的是資料不能太多，因為瀏覽器有4096 bytes限制。
(如果是一些舊瀏覽器就會遇到問題，所以要注意資料就是存識別資料即可)
此外，ASP.NET Core Cookie驗證，在前台會以加密方式儲存，避免使用者去修改，也有自動逾期時間，整體上來安全程度是有的。


### 實作
主要參考 [使用 cookie 驗證但不使用 ASP.NET Core Identity](https://docs.microsoft.com/zh-tw/aspnet/core/security/authentication/cookie?view=aspnetcore-3.1) 、[[ASP.net Core] 實作 Cookie based 登入機制](https://dotblogs.com.tw/shadow/2019/01/16/105615) 這兩篇進行實作，並以7個步驟完成

先列出主要異動的檔案:
(黃色標記處為異動檔案)
{% asset_img "1.png" %}

1.先增加登入逾期的時間定義值

**appsettings.json**
```
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "LoginExpireMinute": 60, //登入逾期時間
  "AllowedHosts": "*"
}
```
+ 第9:<code>LoginExpireMinute</code>表示幾分後逾期登出。

2.到Startup.cs去增加載入的授權、身份驗證服務

**Startup.cs**
```
using Microsoft.AspNetCore.Authentication.Cookies;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.HttpsPolicy;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

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
                // CSRF資安有關，這裡就加入全域驗證範圍Filter的話，待會Controller就不必再加上[AutoValidateAntiforgeryToken]屬性
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
主要改以下幾點:

+ <code>[26~45]:</code>在<code>ConfigureServices</code>追加服務，先取得登入逾期的時間參數LoginExpireMinute。之後追加AddAuthentication服務，並修改AddControllersWithViews內容。
+ <code>[65~66]:</code>啟用身份驗證、授權

3.再來是控制器的部分，主要設定登入、登出、檢視登入後資訊的方能

**HomeController.cs**
```
using CookieAuthentication.Models;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Security.Claims;
using System.Text;
using System.Threading.Tasks;

namespace CookieAuthentication.Controllers
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

        [Authorize]
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

        [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
        public IActionResult Error()
        {
            return View(new ErrorViewModel { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
        }


        [HttpPost]
        public async Task<IActionResult> Login(string Account, string Password,bool IsRememberMe)
        {

            if ((Account == "test01" && Password == "test01pwd") == false)
            {
                ViewBag.errMsg = "帳號或密碼輸入錯誤";
                return View("~/Views/Home/Index.cshtml"); // 登入失敗導回頁面
            }


            // 登入成功，建立驗證 cookie
            Claim[] claims = new[] { new Claim("Account", Account) }; // Key取名"Account"，方便取出後可以對資料庫做操作
            ClaimsIdentity claimsIdentity = new ClaimsIdentity(claims, CookieAuthenticationDefaults.AuthenticationScheme);
            ClaimsPrincipal claimsPrincipal = new ClaimsPrincipal(claimsIdentity);

            // 呼叫 SignInAsync 以登入使用者
            await HttpContext.SignInAsync(claimsPrincipal,
                new AuthenticationProperties()
                {
                    //IsPersistent = false：瀏覽器關閉立馬登出；IsPersistent = true 就變成常見的Remember Me功能
                    IsPersistent = IsRememberMe,
                });

            // 導至隱私頁面
            return RedirectToAction("Privacy", "Home");
        }


        public async Task<IActionResult> Logout()
        {
            await HttpContext.SignOutAsync();
            return RedirectToAction("Index", "Home");// 導至登入頁
        }
    }
}
```
+ 登入:在56~82，主要是檢查帳密>建立Cookie權杖>在使用SignInAsync以Cookie權杖做登入。
+ 登出:在85~89，很簡單的一行<code>await HttpContext.SignOutAsync();</code>即可。
+ 檢視登入後資訊:在33~47，<code>[Authorize]</code>表示授權才能啟用，主要把登入後的資訊從HttpContext中取出給前端顯示。


5.之後建立登入頁，我這把登入做在Index.cshtml

**Index.cshtml**
```
@{
    ViewData["Title"] = "Home Page";
}

<div class="text-center">
    <h1 class="display-4">Welcome</h1>
    <p>Learn about <a href="https://docs.microsoft.com/aspnet/core">building Web apps with ASP.NET Core</a>.</p>
</div>

<!--登入區塊-->
@using (Html.BeginForm("Login", "Home", new { ReturnUrl = Context.Request.Query["ReturnUrl"] }, FormMethod.Post, true, new { name = "loginForm", autocomplete = "off" }))
{
    <div>
        <label>帳號：</label>@Html.TextBox("Account")
    </div>
    <div>
        <label>密碼：</label>@Html.Password("Password")
    </div>
    <div>
        <label>記住我：</label>@Html.CheckBox("IsRememberMe")
    </div>
    <div>
        <button type="submit">提交</button>
    </div>
    <div style="color:red;">
        <!--顯示登入失敗訊息 -->
        @ViewBag.errMsg
    </div>
}
<!--./登入區塊-->

```

6.登入後才可用的頁面，我這把Privacy.cshtml當成是要登入才能進的頁面

**Privacy.cshtml**
```
@{
    ViewData["Title"] = "Privacy Policy";
}
<h1>@ViewData["Title"]</h1>

<p>Use this page to detail your site's privacy policy.</p>

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
+ 第16:實作登出，點下去回連接到HomeController.cs的Logout動作

7.完成，看看登入、登出效果。
{% asset_img "2.gif" %}

### 總結

以這種Cookie-Based驗證方式不用管是DB First還是Code First都可以用，只要簡單做個設定即可!
如果是用Idenity在資料表部分會要符合特定格式，可能對某些已成形的系統不好加入。

最後，在這篇裡面實現了登入、登出，但現在的網站應該還有更細的權限，如:特定功能的新刪修查、某按鈕權限、只能看不能編輯。
因此，在下篇中將會實作更細部分的權限分則。
即以角色為基礎的存取控制（Role-based access control，RBAC）。

(( 這篇文好長

**參考資料**
https://docs.microsoft.com/zh-tw/aspnet/core/security/authorization/introduction?view=aspnetcore-3.1
https://ithelp.ithome.com.tw/articles/10249930
https://blogger.tigernaxo.com/post/dotnetcore31/auth/auth_guild_3/
https://ithelp.ithome.com.tw/articles/10199102
https://ithelp.ithome.com.tw/articles/10198150
https://blog.miniasp.com/post/2019/12/25/asp-net-core-3-cookie-based-authentication
https://dotblogs.com.tw/shadow/2019/01/16/105615
https://docs.microsoft.com/zh-tw/aspnet/core/security/authentication/cookie?view=aspnetcore-3.1
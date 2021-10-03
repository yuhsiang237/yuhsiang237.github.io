---
title: '[Day11] C# MVC傳遞參數的方式 - C#&MVC入門'
date: 2021-10-06 12:06:42
categories:
  - [程式語言,C#]
---
在上回 {% post_link 'C-MVC-檢視元件ViewCompoment使用' '[Day10] C# MVC 檢視元件View Compoment使用- C#&MVC入門' %} ，我們介紹了View Component的做法，算是把整個MVC的View摸透了。

而這回則是要探討如何在這些頁面間傳遞參數。

### Model Binder
主要會透過Model Binder來完成傳遞，Model Binder主要是用於將HTTP request資料對應到controller/Action去。
參數可能是string、integer、class、list、array...等

而為什麼能接到呢?就是前面的這些設定

**Startup.cs**
```
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
});
```

### 常見的傳遞參數方法 

1. 從網址傳遞

例:https://localhost:44378/Home?id=1
```
public IActionResult Index(int id)
{
    Console.Write(id);
    return View();
}
```
後面帶參數即可，以此是id，有多少個帶多少個。

2. 從form post請求

View頁面:
```
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
Controller頁面:
```
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

基本上就是有特別多資料或固定規格會使用Model去接，不然可以參考以下補充資料。

### 補充
通常上面的就足夠來撰寫網頁了，但偶而還是會需要取得請求的特定資料
這時還有以下方法可以用:
Header、Request、Query、Form、Route、FormBody


### 總結
雖然有時會因為避免用戶改資料，把參數傳遞在session storage就是了。
但缺點是他是前台Javascript，要在前台追參數。
最後，當專案大起來時勢必要注意參數的管理，不然A到B頁可能會有各種傳法，在追參數、修改上就會花比較多時間。

**參考資料**
https://ithelp.ithome.com.tw/articles/10240381
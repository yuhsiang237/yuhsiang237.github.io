---
title: '[Day27] C# MVC 回傳獨立頁面或整體頁面 - C#&AspNetCore'
date: 2021-12-27 20:20:46
categories:  
  - [Topic,2021 C# AspNetCore鐵人賽]
  - [Code,C#]
  - [Framework,AspNetCore MVC]
---
在此介紹如何讓某些頁面不套用原本預設的_Layout.cshtml。

如常見的登入頁面，常常是一個獨立畫面，這時只要回傳PartialView。
**UserController.cs**
```
public IActionResult SignIn()
{
    return PartialView();
}
```

而預設會套用_Layout.cshtml的則是回傳View。
**HomeController.cs**
```
public IActionResult Hone()
{
    return View();
}
```

### 結論
簡單介紹一下，此外如果要控制預設吃什麼Layout可以從以下檔案設定:
```
\Views\_ViewStart.cshtml
```
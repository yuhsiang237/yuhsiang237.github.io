---
title: '[Day22] C# MVC API版本控制 - C#&MVC入門'
date: 2021-10-19 19:59:32
categories:
  - [程式語言,C#]
---
在上回中我們介紹了 {% post_link 'C-MVC-RESTful-API-下-實作RESTful-API' '[Day21] C# MVC RESTful API (下) 實作RESTful API - C#&MVC入門' %} ，實作了RESTful API。

而這回將要進行API版本控制!
能處理前端(React/Vue)或APP(iOS/Android)不能馬上更新的最佳手段，有錯可以馬上先還原上一版本、達到向下相容。


### API規格
可修改apiVersion調用不同版本API，如:v1.0、v2.0
<table>
<tbody><tr><th>方法</th>
<th>網址</th>
<th>描述</th>
<th>要求文本</th>
<th>回應文本</th>
</tr><tr>
<td>GET</td>
<td><code>{apiVersion}/api/todo</code></td>
<td>取得所有待辦事項</td>
<td>無</td>
<td>待辦事項陣列</td>
</tr>
<tr>
<td>GET</td>
<td><code>{apiVersion}/api/todo/{id}</code></td>
<td>依識別碼取得待辦事項</td>
<td>無</td>
<td>待辦事項</td>
</tr>
<tr>
<td>POST</td>
<td><code>{apiVersion}/api/todo</code></td>
<td>新增待辦事項</td>
<td>待辦事項</td>
<td>待辦事項</td>
</tr>
<tr>
<td>PUT</td>
<td><code>{apiVersion}/api/todo/{id}</code></td>
<td>更新現有的待辦事項</td>
<td>待辦事項</td>
<td>待辦事項</td>
</tr>
<tr>
<td>DELETE</td>
<td><code>{apiVersion}/api/todo/{id}</code></td>
<td>刪除待辦事項</td>
<td>無</td>
<td>待辦事項</td>
</tr>
<tr>
<td>GET</td>
<td><code>{apiVersion}/api/version</code></td>
<td>取得API版本</td>
<td>無</td>
<td>API版本</td>
</tr>
</tbody></table>


### 實作API版控

先列出我們要改的檔案隻數:
{% asset_img "2.png" %}

1.安裝Microsoft.AspNetCore.Mvc.Versioning
{% asset_img "1.png" %}

2.再來更新Startup.cs，服務增加一行啟用API功能services.AddApiVersioning()
```
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();
    // 資料庫配置
    var connection = @"Server=.\SQLExpress;Database=TodoDB;Trusted_Connection=True;ConnectRetryCount=0";
    services.AddDbContext<TodoDBContext>(options => options.UseSqlServer(connection));
    
    // 啟用API版控功能
    services.AddApiVersioning();

}
```

3.以資料夾分版本層，就是把原本的Controller複製至版本資料夾

如1.0版:
**~/Controllers/V1/VersionController.cs**

```
using System.Collections.Generic;
using Microsoft.AspNetCore.Mvc;

namespace TodoAPI.Controllers.V1
{
    [ApiVersion("1.0")]
    [Route("api/v{version:apiVersion}/[controller]")]
    [ApiController]
    public class VersionController : ControllerBase
    {
        [HttpGet]
        public IEnumerable<string> Get()
        {
            return new string[] { "api v1" };
        }
    }
}

```
如2.0版:

**~/Controllers/V2/VersionController.cs**

```
using System.Collections.Generic;
using Microsoft.AspNetCore.Mvc;

namespace TodoAPI.Controllers.V2
{
    [ApiVersion("2.0")]
    [Route("api/v{version:apiVersion}/[controller]")]
    [ApiController]
    public class VersionController : ControllerBase
    {
        [HttpGet]
        public IEnumerable<string> Get()
        {
            return new string[] { "api v2" };
        }
    }
}
```

只要在每個Controller修改以下的地方即可
```
namespace TodoAPI.Controllers.V1 
```
改成
```
namespace TodoAPI.Controllers.V2
```
以及
```
[ApiVersion("1.0")]
```
改成
```
[ApiVersion("2.0")]
```
TodoController.cs也是一樣喔，就不示範了。

4.嘗試看看
{% asset_img "demo.gif" %}


### 總結
原本很難的事情，竟然可以如此簡單解決!感動!
再來就是考驗DB規劃了，因為如果要向下兼容變成只能增不能刪。
但通常我們能在上與下版間先除錯，所以基本上不會有太大問題。

而LINQ強型別好處就來了，因為是透過Model存取的關係，所以能夠有效避免規格錯誤。

而網址用v1.0、v2.0...原因在Get時瀏覽器只能用網址，所以是最方便的方式。
當然也能夠改放在header、或用?api=1.0的做法。

最後附上完整程式碼:
https://github.com/yuhsiang237/ASP.NET-Core-RESTfulAPI-API-Version-Control

再來就是添加Swagger:
 {% post_link 'C-MVC-Web-API增加Swagger' '[Day23] C# MVC Web API版本增加Swagger - C#&MVC入門' %}

額外補充:
至於Controller多版本那Model呢?
因為向下兼容，可能會回傳不同版的Model
這時就可以使用繼承跟覆寫!
如1.1繼承1.0:ModelV1_1:ModelV1_0
之後添加屬性跟覆寫方法
而主要就是避免資料庫異動太大，就可以避免break change造成不相容。


**參考資料**
https://dotblogs.com.tw/rainmaker/2017/03/12/130759
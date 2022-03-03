---
title: '[Day21] C# MVC RESTful API (下) 實作RESTful API - C#&AspNetCore'
date: 2021-10-18 19:59:32
categories:  
  - [Topic,2021 C# AspNetCore鐵人賽]
  - [Code,C#]
  - [Framework,AspNetCore API]
---
在上回中我們介紹了 {% post_link 'C-MVC-RESTful-API-中-建立API專案' '[Day20] C# MVC RESTful API (中) 建立API專案 - C#&AspNetCore' %} ，我們在原本的MVC專案中加入了WEB API專案。

而這回將會撰寫RESTful API!
本文會以:https://docs.microsoft.com/zh-tw/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio 提供的範例作為實作依據。
### RESTful API規格
<table>
<th>方法</th>
<th>網址</th>
<th>描述</th>
<th>要求文本</th>
<th>回應文本</th>
<tr>
<td>GET</td>
<td><code>/api/todo</code></td>
<td>取得所有待辦事項</td>
<td>無</td>
<td>待辦事項陣列</td>
</tr>
<tr>
<td>GET</td>
<td><code>/api/todo/{id}</code></td>
<td>依識別碼取得待辦事項</td>
<td>無</td>
<td>待辦事項</td>
</tr>
<tr>
<td>POST</td>
<td><code>/api/todo</code></td>
<td>新增待辦事項</td>
<td>待辦事項</td>
<td>待辦事項</td>
</tr>
<tr>
<td>PUT</td>
<td><code>/api/todo/{id}</code></td>
<td>更新現有的待辦事項</td>
<td>待辦事項</td>
<td>待辦事項</td>
</tr>
<tr>
<td>DELETE</td>
<td><code>/api/todo/{id}</code></td>
<td>刪除待辦事項</td>
<td>無</td>
<td>待辦事項</td>
</tr>
<!-- <tr>
<td>POST</td>
<td><code>/api/todo/batch</code></td>
<td>批次處理待辦事項，可批次新增、刪除、編輯</td>
<td>批次動作、待辦事項陣列</td>
<td>批次動作、待辦事項陣列</td>
</tr> -->
<tr>
</table>


### 實作RESTful API
先安裝基本資料庫套件
1.Microsoft.EntityFrameworkCore
{% asset_img "1.png" %}
2.Microsoft.EntityFrameworkCore.SqlServer
{% asset_img "2.png" %}
3.Microsoft.EntityFrameworkCore.Tools
{% asset_img "3.png" %}
4.使用DB First
{% asset_img "4.png" %}

{% asset_img "5.png" %}

用<code>Scaffold-DbContext</code>指令，來還原TodoDB資料庫的Models
```
Scaffold-DbContext "Server=.\SQLExpress;Database=TodoDB;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```
執行畫面:

{% asset_img "6.png" %}
執行後:
{% asset_img "7.png" %}

5.Startup.cs增加資料庫配置
```
// This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();
        // 資料庫配置
        var connection = @"Server=.\SQLExpress;Database=TodoDB;Trusted_Connection=True;ConnectRetryCount=0";
        services.AddDbContext<TodoDBContext>(options => options.UseSqlServer(connection));
    }
```

6.撰寫RESTful API

**~/Controllers/TodoController.cs**
```
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using TodoAPI.Models;

namespace TodoAPI.Controllers
{

    [Route("api/[controller]")]
    [ApiController]
    public class TodoController : ControllerBase
    {
        private readonly TodoDBContext _context;
        public TodoController(TodoDBContext context)
        {
            _context = context;
        }
        [HttpGet]
        public async Task<ActionResult<IEnumerable<Todo>>> GetTodos()
        {
            return await _context.Todos.Where(x=>x.IsDeleted != 1).ToListAsync();
        }

        [HttpGet("{id}")]
        public async Task<ActionResult<Todo>> GetTodo(int id)
        {
            var obj = await _context.Todos.FindAsync(id);

            if (obj.IsDeleted == 1)
            {
                return NotFound();
            }

            return obj;
        }

        [HttpPut("{id}")]
        public async Task<ActionResult<Todo>> PutTodo(int id, Todo newObj)
        {
            if (id != newObj.Id)
            {
                return BadRequest();
            }

            var obj = await _context.Todos.FindAsync(id);

            if(obj.IsDeleted == 1)
            {
                return NotFound();
            }

            obj.IsComplete = newObj.IsComplete;
            obj.Name = newObj.Name;

            try
            {
                await _context.SaveChangesAsync();
            }
            catch (DbUpdateConcurrencyException)
            {
                if (!TodoExists(id))
                {
                    return NotFound();
                }
                else
                {
                    throw;
                }
            }

            return obj;
        }

        [HttpPost]
        public async Task<ActionResult<Todo>> PostTodo(Todo newObj)
        {
            var obj = new Todo
            {
                Name = newObj.Name,
                IsComplete = 0,
                IsDeleted = 0
            };
            _context.Todos.Add(obj); 
            await _context.SaveChangesAsync();

            return CreatedAtAction(nameof(GetTodo), new { id = obj.Id }, obj);
        }
        [HttpDelete("{id}")]
        public async Task<ActionResult<Todo>> DeleteTodo(int id)
        {
            var obj = await _context.Todos.FindAsync(id);
            obj.IsDeleted = 1;

            if (obj == null)
            {
                return NotFound();
            }
            try
            {
                await _context.SaveChangesAsync();
            }
            catch (DbUpdateConcurrencyException)
            {
                if (!TodoExists(id))
                {
                    return NotFound();
                }
                else
                {
                    throw;
                }
            }

            return obj;
        }

        private bool TodoExists(int id)
        {
            return _context.Todos.Any(e => e.Id == id);
        }
    }
}

```
7.用postman測試
{% asset_img "demo.gif" %}


專案程式碼:
https://github.com/yuhsiang237/ASP.NET-Core-RESTfulAPI

### 總結
這樣就完成了RESTful API了!

**參考資料**
https://docs.microsoft.com/zh-tw/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio
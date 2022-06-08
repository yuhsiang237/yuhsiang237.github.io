---
title: '[Day23] C# MVC Web API版本增加Swagger - C#&AspNetCore'
date: 2021-10-26 19:59:32
categories:  
  - [Topic,2021 C# AspNetCore Challenge]
  - [Code,C#]
---
在上回中我們介紹了 {% post_link 'C-MVC-API版本控制' '[Day22] C# MVC API版本控制 - C#&AspNetCore' %} ，實作了RESTful API的版控。

而這回將要進行API的部屬，使用Swagger UI。

找了很多篇算是找到一篇
https://codingfreaks.de/dotnet-core-api-versioning/
可以同時使用API Versioning跟Swagger套件的範例

在這篇中主要配置API並且切版本

1.先用Nuget安裝以下套件:
Swashbuckle.AspNetCore
Swashbuckle.AspNetCore.Annotations
Swashbuckle.AspNetCore.Swagger
Swashbuckle.AspNetCore.SwaggerGen
Swashbuckle.AspNetCore.SwaggerUI

{% asset_img 4.png %}

2.再來是我們要修改的檔案
{% asset_img 5.png %}

首先是Swagger的定義類別

**~/ConfigureSwaggerOptions.cs**
```
namespace ApiVersioningSample
{
    using System;
    using System.Linq;

    using Microsoft.AspNetCore.Mvc.ApiExplorer;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Options;
    using Microsoft.OpenApi.Models;

    using Swashbuckle.AspNetCore.SwaggerGen;

    /// <summary>
    /// Configures the Swagger generation options.
    /// </summary>
    /// <remarks>
    /// <para>
    /// This allows API versioning to define a Swagger document per API version after the
    /// <see cref="IApiVersionDescriptionProvider" /> service has been resolved from the service container.
    /// </para>
    /// <para>Taken from https://github.com/microsoft/aspnet-api-versioning.</para>
    /// </remarks>
    public class ConfigureSwaggerOptions : IConfigureOptions<SwaggerGenOptions>
    {
        #region member vars

        private readonly IApiVersionDescriptionProvider _provider;

        #endregion

        #region constructors and destructors

        /// <summary>
        /// Initializes a new instance of the <see cref="ConfigureSwaggerOptions" /> class.
        /// </summary>
        /// <param name="provider">
        /// The <see cref="IApiVersionDescriptionProvider">provider</see> used to generate Swagger
        /// documents.
        /// </param>
        public ConfigureSwaggerOptions(IApiVersionDescriptionProvider provider)
        {
            _provider = provider;
        }

        #endregion

        #region explicit interfaces

        /// <inheritdoc />
        public void Configure(SwaggerGenOptions options)
        {
            // add a swagger document for each discovered API version
            // note: you might choose to skip or document deprecated API versions differently
            foreach (var description in _provider.ApiVersionDescriptions)
            {
                options.SwaggerDoc(description.GroupName, CreateInfoForApiVersion(description));
            }
        }

        #endregion

        #region methods

        /// <summary>
        /// Internal implementation for building the Swagger basic config.
        /// </summary>
        /// <param name="description">The description object containing the.</param>
        /// <returns>The generated Open API info.</returns>
        private static OpenApiInfo CreateInfoForApiVersion(ApiVersionDescription description)
        {
            var info = new OpenApiInfo
            {
                Title = "Sample API",
                Version = description.ApiVersion.ToString(),
                Description = @"<p>Sample API with versioning including Swagger.</p><p>Partly taken from <a href=""https://github.com/microsoft/aspnet-api-versioning"">this repository</a>.</p>",
                Contact = new OpenApiContact
                {
                    Name = "Yu Hsiang",
                }
            };
            if (description.IsDeprecated)
            {
                info.Description += @"<p><strong><span style=""color:white;background-color:red"">VERSION IS DEPRECATED</span></strong></p>";
            }
            return info;
        }

        #endregion
    }
}
```
這類別主要用來放Swagger客製化的數值

再來是啟用Swagger

**~/Startup.cs**
```
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.HttpsPolicy;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.EntityFrameworkCore;
using TodoAPI.Models;
using Microsoft.OpenApi.Models;
using Swashbuckle.AspNetCore.SwaggerGen;
using Microsoft.Extensions.Options;
using ApiVersioningSample;
using Microsoft.AspNetCore.Mvc.ApiExplorer;

namespace TodoAPI
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
            services.AddControllers();
            // 資料庫配置
            var connection = @"Server=.\SQLExpress;Database=TodoDB;Trusted_Connection=True;ConnectRetryCount=0";
            services.AddDbContext<TodoDBContext>(options => options.UseSqlServer(connection));

            // 啟用API版控功能
            services.AddApiVersioning(
              options =>
              {
                    // reporting api versions will return the headers "api-supported-versions" and "api-deprecated-versions"
                    options.ReportApiVersions = true;
              });

            // swagger 配置
            services.AddVersionedApiExplorer(
                options =>
                {
                    // add the versioned api explorer, which also adds IApiVersionDescriptionProvider service
                    // note: the specified format code will format the version as "'v'major[.minor][-status]"
                    options.GroupNameFormat = "'v'VVV";
                    // note: this option is only necessary when versioning by url segment. the SubstitutionFormat
                    // can also be used to control the format of the API version in route templates
                    options.SubstituteApiVersionInUrl = true;
                });
            services.AddTransient<IConfigureOptions<SwaggerGenOptions>, ConfigureSwaggerOptions>();
            services.AddSwaggerGen();


        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IWebHostEnvironment env, IApiVersionDescriptionProvider provider)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseHttpsRedirection();

            app.UseRouting();

            app.UseAuthorization();
            
            // swagger 配置
            app.UseSwagger();
            app.UseSwaggerUI(
                options =>
                {
                    // build a swagger endpoint for each discovered API version
                    foreach (var description in provider.ApiVersionDescriptions)
                    {
                        options.SwaggerEndpoint($"/swagger/{description.GroupName}/swagger.json", description.GroupName.ToUpperInvariant());
                    }
                });


            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllers();

            });


        }
    }
}

```
第48～60行:swagger 配置
第79～89行:swagger 配置

4.版本的類別撰寫:

**~/Controllers/V1_1/VersionController.cs**
```
using System.Collections.Generic;
using Microsoft.AspNetCore.Mvc;

namespace TodoAPI.Controllers.V1_1
{
    [ApiVersion("1.1")]
    [Route("api/v{version:apiVersion}/[controller]")]
    [ApiController]
    public class VersionController : ControllerBase
    {
        [HttpGet]
        public IEnumerable<string> Get()
        {
            return new string[] { "api v1.1" };
        }
    }
}
```
**~/Controllers/V1_1/VersionController.cs**
```
using System.Collections.Generic;
using Microsoft.AspNetCore.Mvc;

namespace TodoAPI.Controllers.V1_1
{
    [ApiVersion("1.1")]
    [Route("api/v{version:apiVersion}/[controller]")]
    [ApiController]
    public class VersionController : ControllerBase
    {
        [HttpGet]
        public IEnumerable<string> Get()
        {
            return new string[] { "api v1.1" };
        }
    }
}

```
**~/Controllers/V2_0/VersionController.cs**
```
using System.Collections.Generic;
using Microsoft.AspNetCore.Mvc;

namespace TodoAPI.Controllers.V2_0
{
    [ApiVersion("2.0")]
    [Route("api/v{version:apiVersion}/[controller]")]
    [ApiController]
    public class VersionController : ControllerBase
    {
        [HttpGet]
        public IEnumerable<string> Get()
        {
            return new string[] { "api v2.0" };
        }
    }
}

```

基本上就是改
第4行資料夾:V1_0 至 V1_1 至 V2_0
第6行ApiVersion:1.0 至 1.1 至 2.0
其他的Controller也是一樣，這邊就舉例VersionController.cs

5.啟用後範例:
{% asset_img demo.gif %}

### 專案連結
https://github.com/yuhsiang237/ASP.NET-Core-RESTfulAPI-Swagger

### 總覽
Swagger UI搭配版控，而在.net Core官方是沒有提供版本+Swagger配置的，所以踩很多雷。
這回參考了 https://codingfreaks.de/dotnet-core-api-versioning/ 的文章。
花了不少時間在除錯，最後終於把Swagger搭建起來。


**參考資料**
https://codingfreaks.de/dotnet-core-api-versioning/
https://docs.microsoft.com/zh-tw/aspnet/core/tutorials/getting-started-with-swashbuckle?view=aspnetcore-3.1&tabs=visual-studio
https://medium.com/wenchin-rolls-around/%E8%BB%9F%E9%AB%94%E7%89%88%E6%9C%AC%E8%99%9F-ee446e1ad543
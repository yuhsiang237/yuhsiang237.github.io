---
title: '[Day25] C# MVC API JWT Token驗證 (下) JWT實作 - C#&AspNetCore'
date: 2021-10-28 21:57:47
categories:  
  - [Topic,2021 C# AspNetCore Challenge]
  - [Code,C#]  
  - [Framework,AspNetCore API]
---
在這篇中主要實作JWT的登入認證，取得Token、透過Token對有權限的API進行存取。

### 安裝
1.先安裝JWT套件，因為我是用AspNetCore所以用
Microsoft.AspNetCore.Authentication.JwtBearer
{% asset_img "1.png" %}

### 撰寫
1.撰寫產生JWT Token的程式碼。
**TokenController.cs**
```
[HttpPost("GenerateToken")]
[AllowAnonymous]
public ActionResult<string> GenerateToken()
{
    string issuer = "JwtAuthDemo";
    string signKey = "ASASA1@AAASASASASA1@AAASAS"; // 需要大於16字元
    string Account = "TestAccount";
  
    var securityKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(signKey));
    var signingCredentials = new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256Signature);

    // 建立 SecurityTokenDescriptor
    var tokenDescriptor = new SecurityTokenDescriptor
    {
        Issuer = issuer,
        Subject = new ClaimsIdentity(new[]
                {
                  new Claim(JwtRegisteredClaimNames.Sub, Account), // User.Identity.Name
                  new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()), // JWT ID
                  new Claim("test", "123") // 可加入自訂內容在聲明裡
                }),
        Expires = DateTime.Now.AddMinutes(60),
        SigningCredentials = signingCredentials
    };
    var tokenHandler = new JwtSecurityTokenHandler();
    var securityToken = tokenHandler.CreateToken(tokenDescriptor);
    var serializeToken = tokenHandler.WriteToken(securityToken);
    return serializeToken;
}
```

2.撰寫測試JWT請求的程式碼
在此撰寫兩個一個是取得所有聲明，一個是取得該Token得獨立識別值。
**TokenController.cs**
```
[Authorize]
[HttpGet("GetClaims")]
public IActionResult GetClaims()
{
    return Ok(User.Claims.Select(p => new { p.Type, p.Value }));
}

[Authorize]
[HttpGet("GetTokenID")]
public IActionResult GetTokenID()
{
    var jti = User.Claims.FirstOrDefault(p => p.Type == "jti");//每個token的識別值，如果要禁用某token就可以把識別值存到資料庫當黑名單
    return Ok(jti.Value);
}
```
3.增加服務
Startup.cs
```
public void ConfigureServices(IServiceCollection services){
  

  services
  .AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
  .AddJwtBearer(options =>
  {
  // 當驗證失敗時，回應標頭會包含 WWW-Authenticate 標頭，這裡會顯示失敗的詳細錯誤原因
  options.IncludeErrorDetails = true; // 預設值為 true，有時會特別關閉

  options.TokenValidationParameters = new TokenValidationParameters
  {

    // 一般我們都會驗證 Issuer
    ValidateIssuer = true,
    ValidIssuer = "JwtAuthDemo",

    // 通常不太需要驗證 Audience
    ValidateAudience = false,
    //ValidAudience = "JwtAuthDemo", // 不驗證就不需要填寫

    // 一般我們都會驗證 Token 的有效期間
    ValidateLifetime = true,

    // 如果 Token 中包含 key 才需要驗證，一般都只有簽章而已
    ValidateIssuerSigningKey = false,

    // JWT簽證密要，之後可自行設定在appsettings.json
    IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("ASASA1@AAASASASASA1@AAASAS"))
    };
  });

}
```

4.添加授權
```
public void Configure(IApplicationBuilder app, IWebHostEnvironment env, IApiVersionDescriptionProvider provider)
{
        app.UseAuthentication(); 
        app.UseAuthorization();

}
```

5.完成測試結果:
取得Token
{% asset_img "2.png" %}
用剛剛產生的JWT Token取得聲明
{% asset_img "3.png" %}
用剛剛產生的JWT Token取得Token識別值
{% asset_img "4.png" %}


### 總結
C#在處理JWT上算是蠻簡潔的，不用寫太多行，主要複雜點就在Startup.cs增加服務、產生Token這兩個步驟。
專案存庫位置:https://github.com/yuhsiang237/ASP.NET-Core-RESTfulAPI-JWT

然後附上DEMO影片一個是用Postman測試，一個是程式碼跑起的時的Swagger
swagger demo:
{% asset_img "demo.gif" %}
postman demo:
{% asset_img "postmandemo.gif" %}



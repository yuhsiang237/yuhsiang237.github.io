---
title: '[Day24] C# MVC API JWT Token驗證 (上) JWT基礎知識 - C#&AspNetCore'
date: 2021-10-27 21:57:47
categories:
  - [程式語言,C#]
---
在先前有介紹過Cookie-Based驗證於MVC專案裡。
{% post_link 'C-MVC-驗證與授權登入與登出' '[Day12] C# MVC 驗證與授權，登入與登出 - C#&AspNetCore' %}

而這回主要探討的是Token-Based驗證，也就是API常用的JWT (Json Web Token)。

### 什麼是JWT
JSON Web Token (JWT) is a proposed Internet standard for creating data with optional signature and/or optional encryption whose payload holds JSON that asserts some number of claims. 

為JSON Web Token，是一個建議的網路標準，用來建立簽名，且在其中可以視需求加入自訂的JSON內容。

簡言之，就是令牌(Token)，你可以拿這令牌去要資料。

### JWT優點
跨域: 不受網域限制，可用來串接第三方應用，如 OAuth。
安全性: 不使用 Cookie 因此不會受到 CSRF 攻擊，不過 Token 並不能防護 XSS 攻擊，還是需要特別注意。
行動端: 可用於不支援 Cookie 的裝置上，且現在網站和 APP 串接普遍使用 Token 授權。


### JWT格式
主要由三個部分組成:

```
header.payload.signature
```


**Header**
```
{
  "alg": "HS256",
  "typ": "JWT"
}
```
alg:定義加密的方式產生signature，如上是HS256表示使用是HMAC-SHA256.
typ:Token類型

常被使用的header如:typ、cty、alg、kid、x5c、x5u、crit
可自行添加參考:https://en.wikipedia.org/wiki/JSON_Web_Token


**Payload**
```
{
  "loggedInAs": "admin",
  "iat": 1422779638
}
```
loggedInAs:這是客製化claim，以此來說是管理員。
iat:這是標準的claim，表示此JWT發布的時間。

標準的claim如:iss、sub、aud、exp、nbf、iat、jti
可自行添加參考:https://en.wikipedia.org/wiki/JSON_Web_Token

通常為了讓前端或後端好存取多加上一些識別值。

**Signature**
```
HMAC_SHA256(
  secret,
  base64urlEncoding(header) + '.' +
  base64urlEncoding(payload)
)
```
簽名，確保資料完整性的雜湊簽章。

將以上三者用Base64url Encoding結果即為JWT

生成:
```
const token = base64urlEncoding(header) + '.' + base64urlEncoding(payload) + '.' + base64urlEncoding(signature)
```
結果:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsb2dnZWRJbkFzIjoiYWRtaW4iLCJpYXQiOjE0MjI3Nzk2Mzh9.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

### 使用情境
將此Token放置於API請求的Header中:
例如:
```
Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsb2dnZWRJbkFzIjoiYWRtaW4iLCJpYXQiOjE0MjI3Nzk2Mzh9.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```
傳送給後端

### JWT衍伸問題
Token的竊取問題，因為JWT發出去就不能銷毀，只能自己等待過期。
所以有了<code>Refresh Token</code>的出現。

使用Refresh Token後:
縮短每個Token時間，過期時再重新索取。

```
{
  "access_token":"l0XG52TQx",    //Token
  "refresh_token":"KWI3JOkFA",   //Refresh Token
  "expires_in":3600              //幾秒過期
}
```
當Token過期後，使用refresh_token呼叫一個API要取一個新Token。
refresh_token是存在自己資料庫內的，所以被盜用可以馬上刪除掉!

在寫程式碼時可以在某API發現過期後立即呼叫更換。
或是用Token裡面帶入的payload去確認更換時機也可以，但可能會有時區問題。

此外，如果用Refresh Token仍會有一些安全疑慮，就是發出的token還是要等到時間自己逾期。
(不如自己建表記Token還比較安全，所以在下面我寫了額外補充)

### 額外補充
除了JWT外也有一些自訂的做法能用來驗證APP手機端。
如DB自建Token表，可能像是這樣:

每次操作API都帶入Token，DB檢驗一次。
被盜用時想刪隨時能刪、能強制登出。

**Tokens資料表**
<table>
<tr>
<th>UserID</th>
<th>Token</th>
<th>Expires_In</th>
</tr>
<tr>
<td>1</td>
<td>AJIASOIASssarJ</td>
<td>2021-10-16</td>
</tr>
<tr>
<td>2</td>
<td>AASKOPASKOAPaS</td>
<td>2021-10-17</td>
</tr>
<tr>
<td>3</td>
<td>AASK45as4daaS</td>
<td>2021-10-14</td>
</tr>
</table>

流程大概像這樣:
API>撈DB檢查>大於時間，刪除Token>回傳授權失敗

雖然比較耗資料庫資源但我覺得是最安全的方式XD。
而且沒有發出要等到時間到才會逾期的問題

### 總結
在這回中主要探討JWT是什麼、前置的知識理論。
發現了JWT的一些好處與壞處。

因此如果有疑慮好像自己建立Token表還是最安全的XD

而在下篇中將會實作JWT的機制。

**參考資料**
https://ithelp.ithome.com.tw/articles/10199102
https://en.wikipedia.org/wiki/JSON_Web_Token
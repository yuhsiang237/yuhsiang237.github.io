---
title: '[Day19] C# MVC RESTful API (上) RESTful 基礎知識 - C#&AspNetCore'
date: 2021-10-16 10:16:48
categories:  
  - [Topic,2021 C# AspNetCore鐵人賽]
  - [Code,C#]
  - [Framework,AspNetCore API]
---
在上回中我們介紹了 {% post_link 'C-MVC專案中撰寫API' '[Day18] C# MVC專案中撰寫API - C#&AspNetCore' %} ，能在MVC專案中撰寫API了。

而這回，探討Web API，他會開給APP(iOS或Android)、前端網站(React或Vue)這種主從式分離的架構。
為了能開出比較公版的API，就必須先了解RESTful的核心概念。
(這次嘗試以翻原文的方式撰寫，往後都盡可能以原文為主)

### RESTful 介紹
Representational state transfer (REST) is a software architectural style that was created to guide the design and development of the architecture for the World Wide Web.REST defines a set of constraints for how the architecture of an Internet-scale distributed hypermedia system, such as the Web, should behave.The REST architectural style emphasises the scalability of interactions between components, uniform interfaces, independent deployment of components, and the creation of a layered architecture to facilitate caching components to reduce user-perceived latency, enforce security, and encapsulate legacy systems.

REST是一種軟體架構的風格，被創立來引導網站設計、開發架構。並且定義了約束架構，像是在網站上的行為。
架構上強調在組件間的可擴展性、統一的接口、獨立部署的組建、創建分層架構以建立快取減少用戶延遲、強化安全性、封裝老舊系統。

The REST architectural style is designed for network-based applications, specifically client-server applications. But more than that, it is designed for Internet-scale usage, so the coupling between the user agent (client) and the origin server must be as lightweight (loose) as possible to facilitate large-scale adoption. 

被用來處理網路基礎的應用，具體來說在主從式架構的應用。但不止如此，也被用來作為互聯網的使用而設計，因此客戶端、主機端耦合度盡可能可能輕量級，以使在大規模時容易採用。

耦合度(Coupling):coupling is the degree of interdependence between software modules。Coupling is usually contrasted with cohesion. Low coupling often correlates with high cohesion,
耦合度表示模組間相互依賴的程度。
耦合通常與內聚相關，低耦合通常與高內聚相關。
如下的(a)就是比較好的結構，盡量封裝好，開簡單的接口出來，但現實情況下不可能做到理想。
以我自身經驗，曾維護那種(b)的狀況，要搞懂A還得先去搞懂B在幹嘛，追了8層之多。此外，精簡的程式碼不等於高內聚，有時更多的是[忍者程式碼（Ninja Code）](https://lenchen.medium.com/%E5%BF%8D%E8%80%85%E7%A8%8B%E5%BC%8F%E7%A2%BC-ninja-code-cc5e4a70bafc)。
{% asset_img "1.png" %}

### RESTful 6個限制
1. **主從式架構(Client–server architecture)**
    The client-server design pattern enforces the principle of separation of concerns
主從式設計模式強制關注點分離的原則
separating the user interface concerns from the data storage concerns. Portability of the user interface is thus improved. 
分離用戶介面與資料儲存問題。用戶介面的可攜性因此被改善。

2. **無狀態(Statelessness)**
    In computing, a stateless protocol is a communications protocol in which no session information is retained by the receiver, usually a server. Relevant session data is sent to the receiver by the client in such a way that every packet of information transferred can be understood in isolation, without context information from previous packets in the session.
    無狀態是種溝通的協定，沒有session被接收者保留，通常接收者是伺服器端。客戶端將資訊送給接收者，每個資訊可以獨立離解，不用藉由理解上下文才能知道訊息。

3. **快取(Cacheability)**
    As on the World Wide Web, clients and intermediaries can cache responses. Responses must, implicitly or explicitly, define themselves as either cacheable or non-cacheable to prevent clients from providing stale or inappropriate data in response to further requests. Well-managed caching partially or completely eliminates some client–server interactions, further improving scalability and performance.
    在網路上客戶、中間人可以快取結果，可自行定義是否該緩存的資料。良好的快取能改善效能。


4. **分層系統(Layered system)**
    A client cannot ordinarily tell whether it is connected directly to the end server or to an intermediary along the way.Adding security as a separate layer enforces security policies. 
    客戶端不能判斷是連結到終端還是中介。能增加安全性在特別的層級強制特定的安全政策。

5. **按需求編碼(Code on demand) 可選**
    Servers can temporarily extend or customize the functionality of a client by transferring executable code: for example, compiled components such as Java applets, or client-side scripts such as JavaScript.
    伺服器端可臨時擴充或可執行代碼給客戶端，如：客戶端代碼JavaScript、編譯後元件Java小程式。

6. **統一接口(Uniform interface)**
    The uniform interface constraint is fundamental to the design of any RESTful system
    統一接口約束是restful系統的基礎。
    為以下四點:
    1. **請求中包含資源的 ID（Resource identification in requests）** 
        求中包含了各種獨立資源的標識，例如，在Web服務中的URI。資源本身和傳送給客戶端的標識是獨立。例如，伺服器可以將自身的資料庫資訊以HTML、XML或者JSON的方式傳送給客戶端，但是這些可能都不是伺服器的內部記錄方式。
    2. **資源通過標識來操作（Resource manipulation through representations）**
       當客戶端擁有一個資源的標識，包括附帶的元資料，則它就有足夠的資訊來刪除這個資源。
    3. **訊息的自我描述性（Self-descriptive messages）**
        每個訊息包含足夠資訊描述如何處理訊息。
    4. **用超媒體驅動應用狀態（Hypermedia as the engine of application state (HATEOAS)**
        同使用者存取Web伺服器的Home頁面相似，當一個 REST 客戶端存取了最初的REST應用的URI之後，REST 客戶端應該可以使用伺服器端提供的連結，動態的發現所有的可用的資源和可執行的操作。

### RESTful優點

+ 可更高效利用快取來提高回應速度
+ 通訊本身的無狀態性可以讓不同的伺服器的處理一系列請求中的不同請求，提高伺服器的擴充性
+ 瀏覽器即可作為客戶端，簡化軟體需求
+ 相對於其他疊加在HTTP協定之上的機制，REST的軟體相依性更小
不需要額外的資源發現機制
+ 在軟體技術演進中的長期的相容性更好

### HTTP請求方法在RESTful API中的典型應用
<table class="wikitable" border="1">
<tbody><tr>
<th>資源</th>
<th>GET</th>
<th>PUT</th>
<th>POST</th>
<th>DELETE
</th></tr>
<tr>
<th>一組資源的URI，比如<code>https://example.com/resources</code>
</th>
<td><b>列出</b>URI，以及該資源組中每個資源的詳細資訊（後者可選）。
</td>
<td>使用給定的一組資源<b>替換</b>當前整組資源。
</td>
<td>在本組資源中<b>建立/追加</b>一個新的資源。該操作往往返回新資源的URL。
</td>
<td><b>刪除</b>整組資源。
</td></tr>
<tr>
<th>單個資源的URI，比如<code>https://example.com/resources/142</code>
</th>
<td><b>取得</b>指定的資源的詳細資訊，格式可以自選一個合適的網路媒體類型（比如：XML、JSON等）
</td>
<td><b>替換/建立</b>指定的資源。並將其追加到相應的資源組中。
</td>
<td>把指定的資源當做一個資源組，並在其下<b>建立/追加</b>一個新的元素，使其隸屬於當前資源。
</td>
<td><b>刪除</b>指定的元素。
</td></tr></tbody></table>

### RESTful衍伸問題
1. **RESTful的批量處理問題，要一次處理多筆資料**
    RESTful給出了處理單筆資料的方式，卻沒多介紹多筆，因此參考以下文件後發現可以用<code>example.com/resources/batch</code>的方式處理
    https://www.npmjs.com/package/restful-api
    https://cloud.tencent.com/developer/article/1575071
    
2. **RESTful的權限規劃問題，以資源式不好管理**
    後台通常以Role Based Access Control (RBAC) 去規劃權限。
    而RESTful是以資源(Resources)為底，這時判斷哪個API具有某權限就必須特別判斷或添加。如沒限制好可能造成資料多給的漏洞。
    如果是我會以敏感資訊為大方向去限制。
3. **RESTful的客戶端邏輯，錯在誰身上**
    因為關注點分離，所以難知道客戶端如Android/iOS的程式有問題時是落在API人員身上還是前端身上。
    有點我資料都給你了，你自己處理的感覺。

### 總結
在這篇中我們主要探討RESTful的核心理論部分，也發現RESTful在現實使用上的問題。
在我的看法上，使用RESTful的原因在於:
1. API統一的風格設計
2. 有特定設計模式利於維護性
3. 能減少開發成本、開發時間

如果沒法達成以上幾點，那額外使用自訂的API也無妨。
比如Google真有做到全RESTful嗎?
稍微查看Google Drive、Gmail就會發現沒有，還是用一些getXXX的API。只有開給其他開發者才用RESTful API。

因此，直覺上比較適合開給外部的其他人而使用。

最後，實作將在下篇開始進行。

**參考資料**
https://en.wikipedia.org/wiki/Coupling_(computer_programming)#/media/File:CouplingVsCohesion.svg
https://en.wikipedia.org/wiki/Coupling_(computer_programming)
https://zh.wikipedia.org/wiki/%E8%A1%A8%E7%8E%B0%E5%B1%82%E7%8A%B6%E6%80%81%E8%BD%AC%E6%8D%A2
https://cloud.tencent.com/developer/article/1497547
https://cloud.tencent.com/developer/article/1575071
https://www.restapitutorial.com/httpstatuscodes.html#google_vignette
https://noob.tw/restful-api/

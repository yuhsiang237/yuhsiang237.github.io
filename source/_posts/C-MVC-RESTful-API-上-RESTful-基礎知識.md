---
title: '[Day19] C# MVC RESTful API (上) RESTful 基礎知識 - C#&MVC入門'
date: 2021-10-16 10:16:48
categories:
  - [程式語言,C#]
---
在上回中我們介紹了 {% post_link 'C-MVC專案中撰寫API' '[Day18] C# MVC專案中撰寫API - C#&MVC入門' %} ，能在MVC專案中撰寫API了。

而這回，主要探討的是開給其他服務的Web API，像是APP(iOS或Android)、前端網站(React或Vue)。
為了能開出比較公版的API，就必須先了解RESTful API。

### RESTful API
表現層狀態轉換（英語：Representational State Transfer，縮寫：REST）是Roy Thomas Fielding博士於2000年在他的博士論文中提出來的一種全球資訊網軟體架構風格，**目的是便於不同軟體/程式在網路（例如網際網路）中互相傳遞資訊**。

目前在三種主流的Web服務實現方案中，因為REST模式與複雜的SOAP和XML-RPC相比更加簡潔，越來越多的Web服務開始採用REST風格設計和實現。例如，Amazon.com提供接近REST風格的Web服務執行圖書查詢；雅虎提供的Web服務也是REST風格的。

**優點:**

+ 可更高效利用快取來提高回應速度
+ 通訊本身的無狀態性可以讓不同的伺服器的處理一系列請求中的不同請求，提高伺服器的擴充性
+ 瀏覽器即可作為客戶端，簡化軟體需求
+ 相對於其他疊加在HTTP協定之上的機制，REST的軟體相依性更小
+ 不需要額外的資源發現機制
+ 在軟體技術演進中的長期的相容性更好

**REST架構的限制條件:**
1. 客戶端-伺服器（Client-Server）:客戶端-伺服器結構限制的目的是將客戶端和伺服器端的關注點分離。
2. 無狀態（Stateless）:
    + 伺服器不能儲存客戶端的資訊；每一次從客戶端傳送的請求中，要包含所有的必須的狀態資訊，對談資訊由客戶端儲存，伺服器端根據這些狀態資訊來處理請求。
    + 伺服器可以將對談狀態資訊傳遞給其他服務，比如資料庫服務，這樣可以保持一段時間的狀態資訊，從而實現認證功能。
    + 當客戶端可以切換到一個新狀態的時候傳送請求資訊。
    + 當一個或者多個請求被傳送之後，客戶端就處於一個狀態變遷過程中。每一個應用的狀態描述可以被客戶端用來初始化下一次的狀態變遷。
3. 快取（Cacheability）
    + 客戶端和中間的通訊傳遞者可以將回覆快取起來。回覆必須明確的或者間接的表明本身是否可以進行快取，這可以預防客戶端在將來進行請求的時候得到陳舊的或者不恰當的資料。
4. 統一介面（Uniform Interface）
    + 請求中包含資源的 ID（Resource identification in requests）:請求中包含了各種獨立資源的標識，例如，在Web服務中的URI。資源本身和傳送給客戶端的標識是獨立。例如，伺服器可以將自身的資料庫資訊以HTML、XML或者JSON的方式傳送給客戶端，但是這些可能都不是伺服器的內部記錄方式。
    + 資源通過標識來操作（Resource manipulation through representations）:當客戶端擁有一個資源的標識，包括附帶的元資料，則它就有足夠的資訊來刪除這個資源。
    + 訊息的自我描述性（Self-descriptive messages）:每一個訊息都包含足夠的資訊來描述如何來處理這個資訊. 例如，媒體類型 (media-type) 就可以確定需要什麼樣的剖析器來分析媒體資料.
    + 用超媒體驅動應用狀態（Hypermedia as the engine of application state (HATEOAS)）:同使用者存取Web伺服器的Home頁面相似，當一個 REST 客戶端存取了最初的REST應用的URI之後，REST 客戶端應該可以使用伺服器端提供的連結，動態的發現所有的可用的資源和可執行的操作。隨著存取的進行，伺服器在回應中提供文字超連結，以便客戶端可以得到當前可用的操作。客戶端無需用確定的編碼的方式記錄下伺服器端所提供的動態應用的結構資訊。
5. 分層系統（Layered System）
    + 客戶端一般不知道是否直接連接到了最終的伺服器，或者是路徑上的中間伺服器。中間伺服器可以通過負載均衡和共享快取的機制提高系統的可延伸性，這樣可也便於安全策略的部署。
6. 按需代碼（Code-On-Demand，可選）
    + 伺服器可以通過傳送可執行代碼給客戶端的方式臨時性的擴充功能或者客製化功能，例如Java Applet、Flash或JavaScript

(未完...)

### 總結
在這篇中我們主要探討RESTful API的理論部分。
在我的看法上，使用RESTful的原因在於:
1. 簡潔
2. API統一的風格設計
3. 有特定設計模式利於維護

重點不外乎就是希望能減少開發成本、開發時間、維護性。
如果沒法達成以上幾點，那額外使用自訂的API也無妨。
而實作將在下篇開始進行。

**參考資料**
https://docs.microsoft.com/zh-tw/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio
https://docs.microsoft.com/zh-tw/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio
https://ithelp.ithome.com.tw/articles/10241979
https://ithelp.ithome.com.tw/articles/10191925
https://zh.wikipedia.org/wiki/%E8%A1%A8%E7%8E%B0%E5%B1%82%E7%8A%B6%E6%80%81%E8%BD%AC%E6%8D%A2
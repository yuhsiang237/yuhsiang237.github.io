---
title:  '[Day4] SQL Server資料表規劃 - SQL Server資料庫入門'
date: 2021-09-04 16:06:08
categories:
  - [SQL]
---
這回來規劃以訂單表格的資料表。
因為實際情況下可能是PM或SA給你一張範例圖或是Excel印出來表單，要你自己去關聯、建構出整張表。

### 訂單表單
如下:(ps.這訂單來自參考資料的內容)
{% asset_img "訂單.png" 訂單%}

### 檢視訂單表單

**觀察**
1.出現列表，極可能可以獨立成一張表。
2.檢索非列表的內容，看他的來源是否可能來自其他表。

下方是要怎麼把它拆成一張一張的表時我會看的部分，也就是圈出可能成為一張獨立表的關鍵字。
整體是一張訂單。
訂單內有列表，仔細看可以發現是產品列表。
訂單內有客戶相關資訊。

如此一來，就可以得到Orders、Customers、Products、OrderDetail四張表。
往後，也許會因為需求變動造成表格內容不一樣，因此目前就是先抓住大體的框架。
{% asset_img "訂單紅線.png" 訂單標記%}

### 建立資料庫
Database>New Database
{% asset_img "Screenshot 2021-09-04 162949.png" 建立資料庫步驟1%}
Database>New Database
輸出資料庫名稱Orders
{% asset_img "Screenshot 2021-09-04 163147.png" 建立資料庫步驟2%}
可以見到左方出現了Orders資料庫
{% asset_img "Screenshot 2021-09-04 163420.png" 建立資料庫步驟3%}


### 結語
在這回中，我們建立了一個資料庫、把訂單拆成資料表的概念，而下回就是進去重頭戲建立資料表。
其實，在看這張訂單前應該要先有個資料庫正規化的概念。能夠透過他的規則去不斷修正、改進你的資料表。
在下回中，會把資料庫正規化拿出來談一下，同時設計這張訂單的資料表。

**什麼是資料庫正規化?**
避免資料重複或相互矛盾的情形，使資料庫在使用時能更有效率、更容易維護。
資料庫的正規化共可分為1NF (Normal Form)、2NF、3NF、BCNF (Boyce-Codd Normal Form)、4NF、5NF 等多個階段, 不過對於一般資料庫設計來說, 通常只需要執行到BCNF 即可, 而其他更高階的正規化只有在特殊的情況下才用得到。
可以參考這篇:https://ithelp.ithome.com.tw/articles/10229472


**參考資料**
https://ithelp.ithome.com.tw/articles/10214308
http://cc.cust.edu.tw/~ccchen/doc/db_04.pdf
https://ithelp.ithome.com.tw/articles/10229472
https://zh.wikipedia.org/wiki/%E6%95%B0%E6%8D%AE%E5%BA%93%E8%A7%84%E8%8C%83%E5%8C%96
http://140.118.9.79/UML-SASD/%E6%AD%A3%E8%A6%8F%E5%8C%96.pdf

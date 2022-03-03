---
title: '[Day7] SQL server資料庫關聯 - SQL Server資料庫入門'
date: 2021-09-07 23:07:26
categories:
  - [Topic,2021 SQL Server鐵人賽]
  - [Database,SQL Server]
---
在上回中 {% post_link 'SQL-server資料表建立' '[Day6] SQL server資料表建立 - SQL Server資料庫入門' %} 我們建立了正規化後的資料表進資料庫。
在這回中我們要知道資料庫中的關聯關係，並且拉好SQL server中表的主鍵、外來鍵關聯。

### 一對一（One to One）:
有A、B兩個table裡面各自存在一筆記錄，可以使用其主鍵，對應至另一個table的一筆記錄。
如下圖，students_public裡的sid 107000001 在students_private只會出現1筆；students_private裡的sid 107000001在students_public也只會出現1筆。
{% asset_img "一對一範例.png" 一對一範例%}

### 一對多（One to Many）:

如depdepartments的一筆id可以對應到students多筆的dept_id。
所以depdepartments對students是一對多。
相對的，students多筆的dept_id可以對應到depdepartments的一筆id。
所以students對depdepartments是多對一。

{% asset_img "一對多範例.png" 一對多範例%}

### 多對多（Many to Many）:
一個 A 是否會有多個 B？
一個 B 是否會有多個 A？
兩者皆是，就是多對多

以下面例子，
一個文章是否有多個標籤?ANS:是
一個標籤是否有多個文章?ANS:是

這邊稍微解講一下，多對多可能會呈現以下的圖表情況。
posts透過一張中介的表(intermediary table)對應到tags
tags透過一張中介的表(intermediary table)對應到posts
則post與tags為多對多。

(PS.基本上在2NF、3NF時就會不自覺得做出這種表。)

可以參考 {% post_link 'SQL-server資料庫正規化' '
[Day5] 規劃資料表，透過資料庫正規化 - SQL Server資料庫入門
' %} 

{% asset_img "多對多範例.png" 多對多範例%}


ps.圖片來源參考自參考資料。

### 拉資料表關聯
1.建立資料表視圖
{% asset_img "all1.PNG" %}
2.選取要呈現的表
{% asset_img "all2.PNG" %}
3.顯示我們剛剛建立的4張表
{% asset_img "all3.PNG" %}
4.拉關聯
對order_details來說，product_number是外來鍵，而主鍵則是指向products的number。
從products的number拖曳到order_details的product_number即可完成關聯，會跳出視窗點確認後點確定即可。
{% asset_img "拉關聯1.png" %}
5.把剩下的也拉完
對orders來說，customer_number是外來鍵，而主鍵則是指向customers的number。
對order_details來說，order_number是外來鍵，而主鍵則是指向orders的number。
如此一來就完成手動關聯了。
{% asset_img "拉關聯完成.png" %}
6.分析一下資料庫中的表的關聯性

customers、orders
一個顧客有多個訂單?是
一個訂單是否有多個顧客?否
所以customers對orders是一對多

orders、order_details
一個訂單是否有多個訂單明細?是
一個明細是否有多個訂單?否
所以orders對order_details是一對多

orders與products，中間媒介order_details
一個訂單是否有多個產品?是
一個產品是否有多個訂單?是
所以orders對products是多對多



### 結語
在一對一、一對多，我們可以很直覺的知道。
而多對多時可能需要想:
一個 A 是否會有多個 B？
一個 B 是否會有多個 A？
如果回答都是"是"那就是多對多，少一個就是一對多或多對一。

此外，我覺得在規劃時好像不太需要知道一對一、一對多、多對多，只是如果知道在拆表上可能會更快。
因為在2NF、3NF時會不自覺完成，只要知道主鍵、外來鍵即可。

在下回預計會將資料填入資料庫中。

**參考資料:**
https://medium.com/pierceshih/%E7%AD%86%E8%A8%98-%E8%B3%87%E6%96%99%E9%97%9C%E8%81%AF%E7%9A%84%E4%B8%89%E7%A8%AE%E9%97%9C%E4%BF%82-245152c093da
https://notes.andywu.tw/2018/%E8%B3%87%E6%96%99%E5%BA%AB-%E9%97%9C%E8%81%AF%E4%BB%8B%E7%B4%B9-%E4%B8%80%E5%B0%8D%E4%B8%80%E3%80%81%E4%B8%80%E5%B0%8D%E5%A4%9A%E3%80%81%E5%A4%9A%E5%B0%8D%E5%A4%9A/
https://ithelp.ithome.com.tw/articles/10214381
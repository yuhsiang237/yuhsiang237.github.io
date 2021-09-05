---
title:  '[Day6] SQL server資料表建立 - SQL Server資料庫入門'
date: 2021-09-06 00:00:17
categories:
  - [SQL]
---


在上回 {% post_link 'SQL-server資料庫正規化' '
[Day5] 規劃資料表，透過資料庫正規化 - SQL Server資料庫入門
' %} 中，我們經由資料庫正規化1NF->2NF->3NF將訂單表單正規化，再來我們就要使用SSMS(Microsoft SQL Server Management Studio)協助我們在資料庫中建立資料表。

**在這篇中，主要涵蓋內容為:**
1.如何在SQL server中建立資料表
2.如何設定自動遞增
3.欄位型別選擇

**訂單示意圖:**
{% asset_img "訂單.png" 訂單%}

**3NF正規化後:**
{% asset_img "資料表修正.png" 資料表%}




### 建立資料表orders
1.對table點右鍵>new table
{% asset_img "建立資料表1.png" 建立資料表1%}
2.接著會出現設計的框框
{% asset_img "建立資料表2.png" 建立資料表2%}
3.填入欄位與型別
在此，補充一點，金額使用decimal，不要使用float，因為以下原因:
因為float表示近似數值，存在精度損失，存有小數的值可能會造成誤差，因此使用decimal。
decimal(p,s) 需要分別指定小數的最大位數（p）和小數位的數量（s）。
p (precision) ：指定小數的最大位數，小數點的左側和右側的數字的總數量不能超過p，p的取值範圍是從1到38，默認值爲18。
s (scale)：指定在小數點右側的小數位數，p-s是小數點左邊的最大位數。s必須是從0到p的值，只有在指定了精度的情況下才能指定s，s的默認值是0，因此，0 <= s <= p。 
{% asset_img "decimal.png" decimal%}

延伸閱讀: {% post_link 'sql-server資料型態' '[Day3] SQL Server資料型態 - SQL Server資料庫入門' %} 可以多加參考。


{% asset_img "建立資料表3.png" 建立資料表3%}
4.設定主鍵(PK)
{% asset_img "建立資料表4.png" 建立資料表4%}
5.儲存Ctrl+S，並命名為orders
{% asset_img "建立資料表5.png" 建立資料表5%}
6.重新整理，左方會出現orders資料表，代表成功建立
{% asset_img "建立資料表6.png" 建立資料表6%}


### 建立資料表customers
基本上可以照前面做法建立即可。
{% asset_img "customerstable.PNG" customers%}

### 建立資料表products
基本上可以照前面做法建立即可。
{% asset_img "producttable.PNG" customers%}

### 建立資料表order_details
1.建構表
這邊我把amount數量設為整數。
{% asset_img "orderdetailstable1.PNG" customers%}
2.這邊注意，要把id設定為主鍵，之後設定「自動遞增」，作法是identify設定成YES
{% asset_img "orderdetailstable2.PNG" customers%}
3.刷新出現order_details
{% asset_img "orderdetailstable3.PNG" customers%}



### 資料表視圖，總覽資料表
1.建立資料表視圖
{% asset_img "all1.PNG" %}
2.選取要呈現的表
{% asset_img "all2.PNG" %}
3.顯示我們剛剛建立的4張表
{% asset_img "all3.PNG" %}

在跟我們在先前規劃的對照
{% asset_img "資料表修正.png" 資料表%}

### 總結
如果在先前正規化好的話，那建表時真的會很輕鬆XD，就只要考慮型別即可。

欄位命名規則上，我使用「全小寫，搭配底線」的規則(參考Laravel&MySQL)，如果專案有統一就用專案內的，重點是整份是一樣規則就好。ps.此外，我發現用google翻譯翻出來的英文都怪怪的，有些是形容詞、名詞等等，可能要找天弄清楚。


欄位型態上，可參考先前寫的 {% post_link 'sql-server資料型態' '[Day3] SQL Server資料型態 - SQL Server資料庫入門' %} 。
在這回中發現了float與decimal的差別，因此在存有小數的金額請使用decimal，避免造成誤差、不準確。

特殊規則上，如某些欄位是流水號id需要「自動遞增」，作法是identify設定成YES。


在下回，我們將開始關聯這些表。


**參考資料:**
https://ithelp.ithome.com.tw/articles/10214381
https://ithelp.ithome.com.tw/articles/10214308
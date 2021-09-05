---
title: '[Day7] SQL server資料庫關聯 - SQL Server資料庫入門'
date: 2021-09-07 23:07:26
categories:
  - [SQL]
---
在這回中我們要知道資料庫中的關聯關係，。

### 一對一（One to One）:
有A、B兩個table裡面各自存在一筆記錄，可以使用其主鍵，對應至另一個table的一筆記錄。
如下圖，students_public裡的sid 107000001 在students_private只會出現1筆；students_private裡的sid 107000001在students_public也只會出現1筆。
{% asset_img "一對一範例.png" 一對一範例%}

### 一對多（One to One）:

如depdepartments的一筆id可以對應到students多筆的dept_id。
所以depdepartments對students是一對多。
相對的，students多筆的dept_id可以對應到depdepartments的一筆id。
所以students對depdepartments是多對一。

{% asset_img "一對多範例.png" 一對多範例%}

### 多對多（Many to Many）:
A table的一筆記錄可以對應到B table的多筆記錄；
B table的一筆記錄也可以對應到A table的多筆記錄。
這邊稍微解講一下，多對多可能會呈現以下的圖表情況。
posts透過一張中介的表(intermediary table)對應到tags
tags透過一張中介的表(intermediary table)對應到posts
則post與tags為多對多。

基本上我們在2NF、3NF時就會不自覺得做出這種表。

可以參考 {% post_link 'SQL-server資料庫正規化' '
[Day5] 規劃資料表，透過資料庫正規化 - SQL Server資料庫入門
' %} 

{% asset_img "多對多範例.png" 多對多範例%}


ps.圖片來源參考自參考資料。

### 結語
在一對一、一對多，我們可以很直覺的知道。
而多對多時可能需要稍微把表畫出來，才會比較好釐清。
這回先稍微整理，因為有點晚了，明天再補上(未完)。

我覺得在規劃時好像不太需要知道一對一、一對多、多對多，反而只要知道主鍵、外來鍵即可

**參考資料:**
https://medium.com/pierceshih/%E7%AD%86%E8%A8%98-%E8%B3%87%E6%96%99%E9%97%9C%E8%81%AF%E7%9A%84%E4%B8%89%E7%A8%AE%E9%97%9C%E4%BF%82-245152c093da
https://notes.andywu.tw/2018/%E8%B3%87%E6%96%99%E5%BA%AB-%E9%97%9C%E8%81%AF%E4%BB%8B%E7%B4%B9-%E4%B8%80%E5%B0%8D%E4%B8%80%E3%80%81%E4%B8%80%E5%B0%8D%E5%A4%9A%E3%80%81%E5%A4%9A%E5%B0%8D%E5%A4%9A/
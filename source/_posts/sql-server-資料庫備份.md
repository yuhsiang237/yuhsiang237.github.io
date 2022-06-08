---
title: '[Day22] SQL server 資料庫備份與還原，使用bak - SQL Server資料庫入門'
date: 2021-09-17 9:43:48
categories:
  - [Topic,2021 SQL Server Challenge]
  - [Database,SQL Server]
---
在這回主要介紹資料庫的備份方法。
避免哪天真的遇到不可測的失誤、資料庫毀損、硬碟壞掉。
在這篇中先介紹SQL server .bak檔案的備份與還原

### 完整備份
1.對資料表右鍵選到Back Up
{% asset_img "1.png" %}
2.Backup Type 選擇Full表示完整備份
{% asset_img "2.png" %}
3.備份成功會跳出訊息
{% asset_img "3.png" %}
4.可至資料夾找到完整備份的.bak
注意:.bak可能沒辦法向下兼容舊版資料庫，如果要向下兼容要用另一種SQL備份的方法。
{% asset_img "4.png" %}


### 完整備份還原
1.先把資料庫設定成離線狀態，Tasks>Take Offline。
{% asset_img "r1.png" %}
2.勾選Drop All Active Connection，之後按下OK，讓資料庫進入離線模式。
{% asset_img "r2.png" %}
3.之後對資料庫按右鍵Tasks>Restore>Datebase。
{% asset_img "r3.png" %}
4.接下來選擇Device，從本地檔案去還原。
{% asset_img "r4.png" %}
5.在跳出的框框選擇Add
{% asset_img "r5.png" %}
6.選擇剛備份出來的.bak
{% asset_img "r6.png" %}
7.返回原本視窗後按下OK
{% asset_img "r7.png" %}
8.然後點OK，開始進行還原
{% asset_img "r8.png" %}
9.跳出了還原成功
{% asset_img "r9.png" %}
10.檢查可以發現資料表都回來了，裡面的資料也是
{% asset_img "r10.png" %}


### 還原失敗可能情況

1.沒有Offline資料庫，就會造成無法匯入。
錯誤訊息
```
TITLE: Microsoft SQL Server Management Studio
------------------------------

Restore of database 'Orders' failed. (Microsoft.SqlServer.Management.RelationalEngineTasks)

------------------------------
ADDITIONAL INFORMATION:

System.Data.SqlClient.SqlError: Exclusive access could not be obtained because the database is in use. (Microsoft.SqlServer.SmoExtended)

For help, click: https://go.microsoft.com/fwlink?ProdName=Microsoft+SQL+Server&ProdVer=16.100.46437.65+(SMO-master-A)&LinkId=20476

------------------------------
BUTTONS:

OK
------------------------------

```
圖:
{% asset_img "error.png" %}


Ans:只要在還原前先把資料庫設定成離線狀態，Tasks>Take Offline即可。

### 結論
有還原檔案比沒有好，記得備份，以備不時之需。
下篇預計是用SQL方式去產可向下兼容的備份檔案。
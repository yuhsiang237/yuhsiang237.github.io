---
title: '[Day23] SQL server 資料庫備份與還原，使用sql - SQL Server資料庫入門'
date: 2021-09-17 10:36:37
categories:
  - [Topic,2021 SQL Server鐵人賽]
  - [Database,SQL Server]
---
因為SQL server會有匯入舊版問題，像是把2019版的匯入到2008版的，所以要在探討另一個做法，即向下兼容的備份與還原。
### 備份
1.選擇generate scripts
{% asset_img "1.png" %}
2.會跳出框框，選擇進階、儲存成script
{% asset_img "2.png" %}
3.可選擇想降級的SQL server版本
{% asset_img "3.png" %}
4.可選擇只要匯出schema還是data
{% asset_img "4.png" %}
5.點Next下一步
{% asset_img "5.png" %}
6.也是點Next下一步
{% asset_img "6.png" %}
7.等待生成完畢，點OK
{% asset_img "7.png" %}
8.到資料夾就可以找到.sql檔案了
{% asset_img "8.png" %}
### 還原
1.雙擊剛剛匯出的.sql
{% asset_img "8.png" %}
2.執行他Execute
{% asset_img "9.png" %}
3.這時會一直跑都跑不完，中止後發現錯誤，因為用.sql匯入要把原本資料庫給砍了。
不能有相同名稱的資料庫
{% asset_img "10.png" %}
4.把現有資料庫給刪除
{% asset_img "11.png" %}
5.勾選關閉資料庫連線後點擊OK
{% asset_img "12.png" %}
6.此時，重新再跑一次，可以發現成功執行
刷新左方的資料庫，可以發現成功還原了。
{% asset_img "14.png" %}

### 使用.sql的優缺點
優點:
1.可以選擇產出舊版 SQL Server 的 script
2.可以自行調整 script
可能會有問題的部分:
1.特殊字元或是 binary 無法匯入
參考:https://social.technet.microsoft.com/Forums/zh-TW/1fc3c435-5b24-45bf-8cb9-1427e3392de2/293052753023383208032338420837sqlserver2001335722251043439930340?forum=sqlservermanagementzhcht
(但我發現新版2019的都已經有自動幫你補上N了所以好像不太會有問題。)
補上:
在國際性資料庫中管理字元資料，最容易的方式是使用 Unicode nchar、nvarchar 和 nvarchar(max) 資料類型，而不是相對的非 Unicode char、varchar 和 text。

2.如果想要建立相同的資料庫需要調整的內容較多。
(所以我上面做法是直接砍了。)

### 總結
如果.bak不行才出此下策，通常是對方機器太舊才可能用這種方式。


**參考資料**
https://blog.yowko.com/sql-server-backup-restore/
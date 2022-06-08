---
title: '[Day29] Blog資料庫設計參考 - SQL Server資料庫入門'
date: 2021-09-17 21:17:12
categories:
  - [Topic,2021 SQL Server Challenge]
  - [Database,SQL Server]
---
剩下兩篇就30了，後面想看一下其他有規模的資料庫是怎麼設計的。
這回以wordpress為例子，然後注意wordpress是用mysql，所以在型別上會跟SQL server有些不一樣。

### 資料型別上
表的主鍵ID是使用BIGINT，可以存(-9223372036854775808 ~ 9223372036854776807 )
時間則是用DATETIME，MYSQL的DATEIME可以存‘1000-01-01 00:00:00’ to ‘9999-12-31 23:59:59’.
如果是用SQL Server要用DATETIME2，可以存0001-01-01 ~ 9999-12-31
除了主鍵ID外的整數用INT
文字則是VARCHAR
在MYSQL上
TINYTEXT: 256 bytes
TEXT: 65,535 bytes => ~64kb
MEDIUMTEXT: 16,777,215 bytes => ~16MB
LONGTEXT: 4,294,967,295 bytes => ~4GB

### 單一資料表上的設計
wp_comments:
1.透過comment_ID、comment_parent搭配可以設計出樹狀階層留言。
2.發現comment_type是varchar，不是再開一張表用ID關聯回去，就直接存文字。
3.發現有兩種時間comment_date_gmt、comment_date，可能是要處理跨時區問題。
4.發現很多需要另鍵表再做關聯的都直接用varchar，如comment_approved、comment_type。

wp_users:
1.ID是wp_post、wp_comments的外來鍵，可以知道操作者。
2.email只要varchar(100)。

wp_posts:
1.ID是wp_comments的comment_post_ID外來鍵。
2.對日期都多存一個gmt。
3.多存一個comment_count，可能是為了讓要取得留言數不用再做一次sum，讓效能好些。
4.很多需要另鍵表再做關聯的都直接用varchar，如post_status、comment_status、post_type。
5.使用了GUID(255)不知道是要做什麼，因為本身已有個ID可以識別了，且在其他表也沒看到它的存在。
6.有建立時間、修改時間。

wp_term_relationships:
1.object_id、term_taxonomy_id用來做多對多的表，文章、分類。一篇文章有很多個分類，一個分類有很多個文章。

wp_term_taxonomy:
1.ID、parent可以跟留言一樣做出樹狀結構的分類。

wp_options:
1.options_name、options_value，儲存像是字典型別的系統資料，每個資料名稱與數值都1對1。

wp_usermeta、wp_postmeta、wp_commentmata:
1.key、value，儲存字典，用於再網頁上印出metadata，SEO使用。


{% asset_img "wordpress_db_schema.png" %}

### 小結
wordpress很明顯就是文章、留言導向，所以這兩張表設計得比較用心，有多去紀錄一些額外資訊如修改日期。
其他相對不重要的表，則設計的堪用就好，像是一些型別應該要在開個表做個ID在關聯回去，但他直接用varchar文字來做。
此外，考量到跨區問題，所以有多存一個gmt時間，但目前不知道他會怎麼用它。
且一些計算欄位的count都直接開出來，而不是用資料庫算(我是覺得不太好，但如果為了效能是可以加)。
而在多對多的表命名上則是命名relationships，這樣一眼就知道這是個來做關聯用的表。
對於系統定義值以字典方式儲存，就只要key、value，就能避免無限增加的欄了。

最後，多參考一些套裝軟體的資料庫規劃，會更直覺得設計出彈性的資料表，以上。

**參考資料**
https://blog.longwin.com.tw/2009/10/mysql-text-field-type-length-limit-2009/
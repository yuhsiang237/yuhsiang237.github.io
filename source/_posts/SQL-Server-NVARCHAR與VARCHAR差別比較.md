---
title: SQL Server NVARCHAR與VARCHAR差別比較
date: 2022-02-17 10:34:02
categories:
  - [SQL]
---
### 前言
在開資料時在存字串常會有選擇上的問題，到底該存哪種型別?
而在此就稍微介紹NVARCHAR、VARCHAR的差異。
以SQL Server 2019資料庫為準。

### NVARCHAR [ ( n | max ) ]
n表示可變大小字串資料。 n 會定義字串大小 (單位為位元組配對)，且可以是 1 到 4,000 之間的值。max 表示儲存大小上限是 2^30-1 個字元 (2 GB)。 
### VARCHAR [ ( n | max ) ]
使用 n 以位元組為單位來定義字串大小，該值可以介於 1 到 8,000，或使用 max 來表示資料行大小限制最多為 2^31-1 個位元組 (2 GB) 的儲存體。 

<table>
<tr>
<td></td>
<td>NVARCHAR</td>
<td>VARCHAR</td>
</tr>
<tr>
<td>Unicode支援</td>
<td>有</td>
<td>無</td>
</tr>
<tr>
<td>每個字元儲存容量</td>
<td>英文字元2 Byte;非英文字元2 Byte</td>
<td>英文字元1 Byte;非英文字元2 Byte</td>
</tr>
<tr>
<td>儲存長度</td>
<td>不定長，依照資料內容</td>
<td>不定長，依照資料內容</td>
</tr>
<tr>
<td>n的長度限制</td>
<td>1~4000(表示可以用寫4000個中、英文字元)</td>
<td>1~8000(表示可以用寫8000個英文字元)</td>
</tr>
<tr>
<td>max</td>
<td>2GB</td>
<td>2GB</td>
</tr>
<tr>
<td>結論</td>
<td>要儲存英文以外的用NVARCHAR，可以避免亂碼問題，但儲存空間相較VARCHAR會多一些。</td>
<td>只存英文VARCHAR沒問題，存其他可能會有亂碼問題。</td>
</tr>
</table>

### 總結
使用NVARCHAR來儲存字串會比較好，因為可避免字串因為中英混雜亂碼。

### 參考資料
https://docs.microsoft.com/zh-tw/sql/t-sql/data-types/char-and-varchar-transact-sql?view=sql-server-ver15
https://docs.microsoft.com/zh-tw/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?view=sql-server-ver15
https://ithelp.ithome.com.tw/articles/10213922

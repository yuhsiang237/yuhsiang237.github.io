---
title: '[Day3] SQL Server資料型態 - SQL Server資料庫入門'
date: 2021-09-03 18:33:36
categories:
  - [SQL]
---
為了能夠讓每種資料有比較好的儲存方式，所以這回來看一下SQL server內建哪些資料型態。
我們平常常見資料可能會有日期、數字、文字等等。
爬了很多文後發現SQL Server的資料型態真的爆多，所以先列舉，未來如果有用到會陸續回來修補這篇文(X)。


### 整數
<table style="text-align: left; height: 113px;" width="550" border="1">
<tbody>
<tr>
<th><span style="font-size: 12pt;">資料型別</span></th>
<th><span style="font-size: 12pt;">位元數</span></th>
<th><span style="font-size: 12pt;">資料範圍</span></th>
</tr>
<tr>
<td><span style="font-size: 12pt;">tinyint</span></td>
<td><span style="font-size: 12pt;">&nbsp;1 Byte</span></td>
<td><span style="font-size: 12pt;">0 ~ 2^8 -1<br>( 0 ~ 255 )</span></td>
</tr>
<tr>
<td><span style="font-size: 12pt;">smallint</span></td>
<td><span style="font-size: 12pt;">&nbsp;2 Bytes</span></td>
<td><span style="font-size: 12pt;">-2^15 ~ 2^15-1<br>( -32738 ~ 32767 )</span></td>
</tr>
<tr>
<td><span style="font-size: 12pt;">int</span></td>
<td><span style="font-size: 12pt;">&nbsp;4 Bytes</span></td>
<td>
<p><span style="font-size: 12pt;">-2^31 ~ 2^31 -1<br>( -2147483648 ~ 2147483467)</span></p>
</td>
</tr>
<tr>
<td><span style="font-size: 12pt;">bigint</span></td>
<td><span style="font-size: 12pt;">&nbsp;8 Bytes</span></td>
<td><span style="font-size: 12pt;">-2^63~2^63 -1<br>
<span style="font-size: 10pt;">(-9223372036854775808 ~ 9223372036854776807 )</span></span></td>
</tr>
<tr>
<td><span style="font-size: 12pt;">bit</span></td>
<td><span style="font-size: 12pt;">&nbsp;1 Byte</span></td>
<td><span style="font-size: 12pt;">&nbsp;0 , 1 , Null</span></td>
</tr>
</tbody>
</table>

### 精確位數與浮點數
<table style="height: 94px;" width="550" border="1">
<tbody>
<tr>
<th><span style="font-size: 12pt;">資料型別</span></th>
<th><span style="font-size: 12pt;">位元數</span></th>
<th><span style="font-size: 12pt;">資料範圍</span></th>
<th><span style="font-size: 12pt;">備註</span></th>
</tr>
<tr>
<td><span style="font-size: 12pt;">numeric</span></td>
<td><span style="font-size: 12pt;">視精確度而定</span></td>
<td><span style="font-size: 12pt;">-10^38 +1 ~ 10^38 -1<br></span></td>
<td></td>
</tr>
<tr>
<td><span style="font-size: 12pt;">decimal</span></td>
<td><span style="font-size: 12pt;">視精確度而定</span></td>
<td><span style="font-size: 12pt;">-10^38 +1 ~ 10^38 -1</span></td>
<td>如果要存有小數點的錢、金額請使用decimal，不要使用float，避免造成誤差<br/>
decimal(p,s) 需要分別指定小數的最大位數（p）和小數位的數量（s）。
p (precision) ：指定小數的最大位數，小數點的左側和右側的數字的總數量不能超過p，p的取值範圍是從1到38，默認值爲18。
s (scale)：指定在小數點右側的小數位數，p-s是小數點左邊的最大位數。s必須是從0到p的值，只有在指定了精度的情況下才能指定s，s的默認值是0，因此，0 <= s <= p。 
{% asset_img "decimal.png" decimal%}

</td>
</tr>
<tr>
<td><span style="font-size: 12pt;">float</span></td>
<td><span style="font-size: 12pt;">&nbsp;8 Bytes</span></td>
<td><span style="font-size: 12pt;">&nbsp;-1.79E+308 ~ 1.79E+308<br>15 位數<br></span></td>
<td>不要使用float存小數點的錢，避免造成誤差。他適合儲存科學用的數字。</td>
</tr>
<tr>
<td><span style="font-size: 12pt;">real</span></td>
<td><span style="font-size: 12pt;">&nbsp;4 Bytes<br></span></td>
<td><span style="font-size: 12pt;">-3.40E+38 ~ 3.40E+38<br>&nbsp;
7 位數</span></td>
<td></td>
</tr>
</tbody>
</table>

### 字串 - Unicode 字串 - Binary 字串
<table style="height: 166px;" width="550" border="1">
<tbody>
<tr>
<th><span style="font-size: 12pt;">資料型別</span></th>
<th><span style="font-size: 12pt;">位元數</span></th>
<th><span style="font-size: 12pt;">資料範圍</span></th>
<th><span style="font-size: 12pt;">備註</span></th>
</tr>
<tr>
<td><span style="font-size: 12pt;">&nbsp;char(n)</span></td>
<td><span style="font-size: 12pt;">1字元 1Byte</span></td>
<td><span style="font-size: 12pt;">&nbsp;1 ~ 8000 字元</span></td>
<td>輸入的數據長度沒有達到指定的長度時將自動以英文空格在其後面填充，讓長度達到相對應的長度。</td>
</tr>
<tr>
<td><span style="font-size: 12pt;">&nbsp;varchar(n)</span></td>
<td><span style="font-size: 12pt;">1字元 1Byte</span></td>
<td><span style="font-size: 12pt;">&nbsp;1 ~ 8000 字元</span></td>
<td><br>輸入的數據長度沒有達到指定的長度時不會在後面填充空格。</td>
</tr>
<tr>
<td><span style="font-size: 12pt;">&nbsp;varchar(max)</span></td>
<td><span style="font-size: 12pt;">變動長度 max=2GB</span></td>
<td><span style="font-size: 12pt;">&nbsp;1 ~ 2^31 -1 字元<br/></span></td>
<td>輸入的數據長度沒有達到指定的長度時不會在後面填充空格。</td>
</tr>
<tr>
<td><span style="font-size: 12pt;">&nbsp;text</span></td>
<td><span style="font-size: 12pt;">變動長度 max=2GB</span></td>
<td><span style="font-size: 12pt;">&nbsp;1 ~ 2^31 -1 字元</span></td>
<td></td>

</tr>
<tr>
<td><span style="font-size: 12pt;">&nbsp;nchar(n)</span></td>
<td><span style="font-size: 12pt;">1字元 2Byte</span></td>
<td><span style="font-size: 12pt;">&nbsp;1 ~ 4000 字元</span></td>
<td>所有的文字都用2 Byte來儲存<br>輸入的數據長度沒有達到指定的長度時將自動以英文空格在其後面填充，讓長度達到相對應的長度。</td>

</tr>
<tr>
<td><span style="font-size: 12pt;">&nbsp;nvarchar(n)</span></td>
<td><span style="font-size: 12pt;">1字元 2Byte</span></td>
<td><span style="font-size: 12pt;">&nbsp;1 ~ 4000 字元</span></td>
<td>所有的文字都用2 Byte來儲存<br>輸入的數據長度沒有達到指定的長度時不會在後面填充空格。</td>
</tr>
<tr>
<td><span style="font-size: 12pt;">&nbsp;nvarchar(max)</span></td>
<td><span style="font-size: 12pt;">1字元 2Byte</span><br><span style="font-size: 12pt;">變動長度 max=2GB</span></td>
<td><span style="font-size: 12pt;">&nbsp;1 ~ 2^30 -1 字元</span></td>
<td>所有的文字都用2 Byte來儲存<br>輸入的數據長度沒有達到指定的長度時不會在後面填充空格。</td>

</tr>
<tr>
<td><span style="font-size: 12pt;">&nbsp;ntext</span></td>
<td><span style="font-size: 12pt;">1字元 2Byte</span><br><span style="font-size: 12pt;">變動長度 max=2GB</span></td>
<td><span style="font-size: 12pt;">&nbsp;1 ~ 2^30 -1 字元</span></td>
<td></td>
</tr>
<tr>
<td><span style="font-size: 12pt;">&nbsp;binary(n)</span></td>
<td><span style="font-size: 12pt;">固定長度 8000 Bytes,不足自動補上 0x00<br></span></td>
<td><span style="font-size: 12pt;">&nbsp;1 ~ 8000 Bytes</span></td>
<td></td>

</tr>
<tr>
<td><span style="font-size: 12pt;">&nbsp;varbinary(n)</span></td>
<td><span style="font-size: 12pt;">變動長度</span></td>
<td><span style="font-size: 12pt;">&nbsp;1 ~ 8000 Bytes</span></td>
<td></td>

</tr>
<tr>
<td><span style="font-size: 12pt;">&nbsp;varbinary(max)</span></td>
<td><span style="font-size: 12pt;">變動長度 max=2GB</span></td>
<td><span style="font-size: 12pt;">&nbsp;1 ~ 2^31 -1 Bytes</span></td>
<td></td>

</tr>
<tr>
<td><span style="font-size: 12pt;">&nbsp;image</span></td>
<td><span style="font-size: 12pt;">變動長度 max=2GB</span></td>
<td><span style="font-size: 12pt;">&nbsp;1 ~ 2^31 -1 Bytes</span></td>
<td></td>

</tr>
</tbody>
</table>

### 日期 - 時間 - 貨幣 - Timestamp
<table style="height: 94px;" width="550" border="1">
<tbody>
<tr>
<th><span style="font-size: 12pt;">資料型別</span></th>
<th><span style="font-size: 12pt;">位元數</span></th>
<th><span style="font-size: 12pt;">資料範圍</span></th>
</tr>
<tr>
<td><span style="font-size: 12pt;">datatime</span></td>
<td><span style="font-size: 12pt;">&nbsp;8 Bytes</span></td>
<td><span style="font-size: 12pt;">1753/1/1 ~ 9999/12/31<br>ex: 2008-11-27 08:08:08.888<br></span></td>
</tr>
<tr>
<td><span style="font-size: 12pt;">datatime2</span></td>
<td><span style="font-size: 12pt;">6 bytes for precision less than 3.<br>
7 bytes for precision 3 or 4.<br>
All other precision require 8 bytes.2<br></span></td>
<td><span style="font-size: 12pt;">0001-01-01 ~ 9999-12-31</span></td>
</tr>
<tr>
<td><span style="font-size: 12pt;">smalldatatime</span></td>
<td><span style="font-size: 12pt;">&nbsp;4 Bytes<br></span></td>
<td><span style="font-size: 12pt;">1900/1/1 ~ 2079/6/6<br>ex: 2008-11-24 15:11<br></span></td>
</tr>
<tr>
<td><span style="font-size: 12pt;">money</span></td>
<td><span style="font-size: 12pt;">&nbsp;8 Bytes</span></td>
<td><span style="font-size: 12pt;">&nbsp;-2^63 ~ 2^63 -1 </span><span style="font-size: 12pt;">小數4位</span><br><span style="font-size: 12pt;">( -922337203685477.5808 ~ 922337203685477.5807)<br></span></td>
</tr>
<tr>
<td><span style="font-size: 12pt;">smallmoney</span></td>
<td><span style="font-size: 12pt;">&nbsp;4 Bytes<br></span></td>
<td><span style="font-size: 12pt;">-2^31 ~ 2^31 -1 小數4位&nbsp; <br>( -214748.3648 ~ 214748.3647)&nbsp;</span></td>
</tr>
<tr>
<td><span style="font-size: 12pt;">timestamp</span></td>
<td><span style="font-size: 12pt;">&nbsp;8 Bytes</span></td>
<td><span style="font-size: 12pt;">8 Bytes 的&nbsp; 16 進位值</span><span style="font-size: 12pt;"><br></span></td>
</tr>
<tr>
<td><span style="font-size: 12pt;">uniqueidentifier</span></td>
<td><span style="font-size: 12pt;">&nbsp;16 Bytes<br></span></td>
<td><span style="font-size: 12pt;">16 Bytes 的 16 進位值</span></td>
</tr>
</tbody>
</table>

### 結語

這回以SQL Server的資料型別列舉出資料範圍，對於日後再選擇時就能稍微留意空間大小、資料的範圍等規劃問題。
自己在使用上則是字串nvarchar，整數int、浮點數float、日期datetime2，就能解決大多問題。
而SQL Server沒有MySQL的boolean型態，要改用0、1去存，可以考慮tinyint。
而在不同時區的資料則是統一都用一個標準去儲存日期UTC時間，如果有機會未來會在寫一篇跨國時間可能會遇到的問題。




**參考資料**
https://docs.microsoft.com/zh-tw/sql/t-sql/data-types/data-types-transact-sql?view=sql-server-ver15
https://ithelp.ithome.com.tw/articles/10203456
https://blog.xuite.net/tolarku/blog/20968398-SQL+Data+Type+%E8%B3%87%E6%96%99%E5%9E%8B%E5%88%A5
https://docs.microsoft.com/en-us/sql/t-sql/data-types/datetime2-transact-sql?view=sql-server-ver15
https://ithelp.ithome.com.tw/articles/10213922
https://ithelp.ithome.com.tw/articles/10203456?sc=iThelpR
https://malagege.github.io/blog/2018/11/09/DB%E5%88%B0%E5%BA%95%E6%9C%89%E6%B2%92%E6%9C%89boolean%E5%9E%8B%E6%85%8B%E5%91%A2/
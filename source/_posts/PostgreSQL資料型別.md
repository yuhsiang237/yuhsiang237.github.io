---
title: PostgreSQL資料型別
date: 2022-03-03 13:23:41
categories:
  - [Database,PostgreSQL]
---
### 前言
因為最近有碰PostgreSQL的需要，因此來整理儲存型別。
此外，發現PostgreSQL的欄位型態具有Alias別名，所以有些型別找不到就要參考文件的Alias。
在以下只列常見的型別，更多請自己參閱參考資料。

### 常見資料型別

#### 字串
<table>
<tr>
<td>
名稱
</td>
<td>
描述
</td>
<td>
備註
</td>
</tr>
<tr>
<td>character varying(n), varchar(n)
</td>
<td>可變長度，但有限制
</td><td>其中 n 是正整數。可以長度最多為 n 個字元（不是位元組）的字串。</td>
</tr>

<tr>
<td>character(n), char(n)
</td>
<td>固定長度，空白填充
</td><td>其中 n 是正整數。可以長度最多為 n 個字元（不是位元組）的字串。</td>
</tr>


<tr>
<td>text
</td>
<td>可變且無限長度
</td><td></td>
</tr>

</table>

#### 數字
<table>
<tr>
<td>
名稱
</td>
<td>
描述
</td>
<td>
備註
</td>
</tr>
<tr>
<td>smallint
</td>
<td>2 bytes
，-32768 to +32767
</td><td></td>
</tr>

<tr>
<td>integer
</td>
<td>4 bytes，-2147483648 to +2147483647
</td><td></td>
</tr>


<tr>
<td>bigint
</td>
<td>8 bytes，-9223372036854775808 to +9223372036854775807
</td><td></td>
</tr>

<tr>
<td>
numeric
</td>
<td>up to 131072 digits before the decimal point; up to 16383 digits after the decimal point。可調式精確度數值型別

</td><td>Alias decimal</td>
</tr>

<tr>
<td>
real
</td>
<td>4Bytes，6 decimal digits precision
非精度浮點數型別
</td><td></td>
</tr>

<tr>
<td>
double precision

</td>
<td>8Bytes，15 decimal digits precision
非精度浮點數型別
</td><td></td>
</tr>
</table>

#### 布林
<table>
<tr>
<td>
名稱
</td>
<td>
描述
</td>
<td>
備註
</td>
</tr>
<tr>
<td>boolean
</td>
<td>1Bytes
</td><td></td>
</tr>
</table>


#### 日期時間
<table>
<tr>
<td>
名稱
</td>
<td>
描述
</td>
<td>
備註
</td>
</tr>
<tr>
<td>timestamp [ (p) ] [ without time zone ]
</td>
<td>8 bytes，
both date and time (no time zone)，
4713 BC ~
294276 AD
</td><td></td>
</tr>

<tr>
<td>
timestamp [ (p) ] with time zone
</td>
<td>8 bytes，
both date and time, with time zone，
4713 BC ~
294276 AD
</td><td></td>
</tr>
</table>

### 總結
在這篇中以一個熟悉SQL Server的角度，列出了平常可能會見到的PostgreSQL資料型態。
因為後者的資料型別彈性高(能夠自己建立物件存進、支援儲存JSON等等)，所以等後面實際碰到再額外補充。
而完整的型別請自行參考網址的參考資料吧!

可能會有人有疑問怎沒SQL Server的NVARCHAR?
Generally you just use a UTF-8 encoded DB, and everything works with no hassle.

### 參考資料
- https://www.postgresqltutorial.com/postgresql-data-types/
- https://docs.postgresql.tw/the-sql-language/data-types
- https://www.itread01.com/content/1550538018.html
- https://stackoverflow.com/questions/21925802/can-i-use-nvarchar-data-type-in-plpgsql

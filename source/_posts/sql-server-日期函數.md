---
title:  '[Day25] SQL server 日期函數 - SQL Server資料庫入門'
date: 2021-09-17 16:28:08
categories:
  - [Topic,2021 SQL Server Challenge]
  - [Database,SQL Server]
---
這回介紹日期函數的使用。
在大多的資料庫都會有一些可以用的函式，讓我們做一些特殊操作。
在這回以SQL server資料庫為例子。

### SQL server日期函式
GETDATE() 取得現在的日期時間
DATEADD() 增加或減少指定的時間間隔
DATEPART() 取出日期時間中特定的部分
CONVERT() 格式化日期時間顯示
DATEDIFF() 日期相減

### 可用的關鍵字
可用的關鍵字參數:
<table><thead><tr><th>datepart (全名和縮寫)</th><th>說明</th></tr></thead><tbody><tr><td>year, yyyy, yy</td><td>年</td></tr><tr><td>month, mm, m</td><td>月</td></tr><tr><td>day, dd, d</td><td>日</td></tr><tr><td>hour, hh</td><td>時</td></tr><tr><td>minute, n</td><td>分</td></tr><tr><td>second, ss, s</td><td>秒</td></tr><tr><td>millisecond, ms</td><td>毫秒</td></tr><tr><td>microsecond, mcs</td><td>微秒</td></tr><tr><td>nanosecond, ns</td><td>毫微秒</td></tr><tr><td>quarter, qq, q</td><td>季</td></tr><tr><td>dayofyear, dy, y</td><td>一年中的第幾天</td></tr><tr><td>week, wk, ww</td><td>週</td></tr><tr><td>weekday, dw</td><td>星期幾</td></tr></tbody></table>


### GETDATE
取得現在的日期時間
```
Select 
GETDATE() as 現在的日期時間
```
結果
{% asset_img "1.png" %}

### DATEADD
增加或減少指定的時間間隔
可以輸入負值，代表回推
正值則是往後推
```
Select 
DATEADD(day, 2, GETDATE()) as 兩天後
```
結果
{% asset_img "2.png" %}


### DATEPART
取出日期時間中特定的部分
```
SELECT DATEPART(yyyy, GETDATE())  as 年,
DATEPART(mm, GETDATE())  as 月,
DATEPART(dd, GETDATE())  as 日
```
結果
{% asset_img "3.png" %}


### CONVERT
格式化日期時間顯示，可以用格式化代碼快速轉成指定規範格式。

格式代碼:
<table>
<tbody><tr>
<th>Style ID</th>
<th>Style 格式</th>
</tr>

<tr>
<td>100 或者 0</td>
<td>mon dd yyyy hh:miAM （或者 PM）</td>
</tr>

<tr>
<td>101</td>
<td>mm/dd/yy</td>
</tr>

<tr>
<td>102</td>
<td>yy.mm.dd</td>
</tr>

<tr>
<td>103</td>
<td>dd/mm/yy</td>
</tr>

<tr>
<td>104</td>
<td>dd.mm.yy</td>
</tr>

<tr>
<td>105</td>
<td>dd-mm-yy</td>
</tr>

<tr>
<td>106</td>
<td>dd mon yy</td>
</tr>

<tr>
<td>107</td>
<td>Mon dd, yy</td>
</tr>

<tr>
<td>108</td>
<td>hh:mm:ss</td>
</tr>

<tr>
<td>109 或者 9</td>
<td>mon dd yyyy hh:mi:ss:mmmAM（或者 PM）</td>
</tr>

<tr>
<td>110</td>
<td>mm-dd-yy</td>
</tr>

<tr>
<td>111</td>
<td>yy/mm/dd</td>
</tr>

<tr>
<td>112</td>
<td>yymmdd</td></tr>

<tr>
<td>113 或者 13</td>
<td>dd mon yyyy hh:mm:ss:mmm(24h)</td>
</tr>

<tr>
<td>114</td>
<td>hh:mi:ss:mmm(24h)</td>
</tr>

<tr>
<td>120 或者 20</td>
<td>yyyy-mm-dd hh:mi:ss(24h)</td>
</tr>

<tr>
<td>121 或者 21</td>
<td>yyyy-mm-dd hh:mi:ss.mmm(24h)</td>
</tr>

<tr>
<td>126</td>
<td>yyyy-mm-ddThh:mm:ss.mmm（没有空格）</td>
</tr>

<tr>
<td>130</td>
<td>dd mon yyyy hh:mi:ss:mmmAM</td>
</tr>

<tr>
<td>131</td>
<td>dd/mm/yy hh:mi:ss:mmmAM</td>
</tr>
</tbody></table>
```
SELECT CONVERT(varchar, GETDATE(), 100) AS Converted
```
結果
{% asset_img "4.png" %}

### DATEDIFF
日期相減

```
SELECT DATEDIFF(day, '2008-06-10', '2008-08-10') AS 日期相差
```
結果
{% asset_img "5.png" %}


### 結論
在SQL語法上比較常用的是DATEDIFF、DATEADD、CONVERT。GETDATE。
如:前1個月、今天-7天、上禮拜資料這種需求還蠻常見的。

**參考資料**
https://www.fooish.com/sql/sql-server-getdate-function.html
https://www.w3school.com.cn/sql/func_convert.asp
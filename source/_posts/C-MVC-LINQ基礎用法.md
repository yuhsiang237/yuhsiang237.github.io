---
title: '[Day14] C# MVC LINQ基礎用法 - C#&AspNetCore'
date: 2021-10-10 19:20:46
categories:  
  - [Topic,2021 C# AspNetCore鐵人賽]
  - [Code,C#]
  - [Framework,AspNetCore MVC]
---
在上回 {% post_link 'C-MVC-驗證與授權，新刪修查按鈕權限' '[Day13] C# MVC 驗證與授權，新刪修查按鈕權限 - C#&AspNetCore'%}  ，我們學會了進階的權限切割。

而這回就要開始操作資料了，使用LINQ。

### LINQ
Language Integrated Query (LINQ) 是一組以直接將查詢功能整合至 C# 語言為基礎之技術的名稱。 傳統上，資料查詢是以簡單的字串表示，既不會在編譯時進行類型檢查，也不支援 IntelliSense。 此外，您還必須針對每種資料來源類型學習不同的查詢語言：SQL 資料庫、XML 文件、各種 Web 服務等等。透過 LINQ，查詢會是第一級語言建構，和類別、方法及事件相同。 您可以使用語言關鍵字和熟悉的運算子，針對強型別的物件集合撰寫查詢。 LINQ 技術系列會針對物件 (LINQ to Object)、關聯式資料庫 (LINQ to SQL) 與 XML (LINQ to XML)，提供一致的查詢體驗。

對於撰寫查詢的開發人員來說，LINQ 最明顯的「語言整合」部分就是查詢運算式。 查詢運算式是以宣告式「查詢語法」撰寫。 透過使用查詢語法，您就可以利用最少的程式碼，針對資料來源執行篩選、排序及分組作業。 您可以使用相同的基本查詢運算式模式來查詢和轉換 SQL 資料庫、ADO.NET 資料集、XML 檔和資料流程和 .net 集合中的資料。

您可以使用 C# 針對下列項目撰寫 LINQ 查詢：SQL Server 資料庫、XML 文件、ADO.NET 資料集，以及支援 IEnumerable 或泛型 IEnumerable<T> 介面的任何物件集合。 也有協力廠商針對許多 Web 服務和其他資料庫實作提供 LINQ 支援。

所有 LINQ 查詢作業都包含三個不同的動作：
1. 取得資料來源。
2. 建立查詢。
3. 執行查詢。

### 實作


**HomeController.cs**

```
public IActionResult Index()
{
    // Specify the data source.
    int[] scores = new int[] { 97, 92, 81, 60 };

    // Define the query expression.
    IEnumerable<int> scoreQuery =
        from score in scores
        where score > 80
        select score;

    // Execute the query.
    foreach (int i in scoreQuery)
    {
        Console.Write(i + " ");
    }
    return View();
}
```
說明:
以上就是過濾>80的分數。

+ IEnumerable:第7行，公開能逐一查看非泛型集合內容一次的列舉程式。而其實可以用var當作宣告替代。
+ scoreQuery:過濾的結果。
{% asset_img "1.png" %}
下圖顯示完整的查詢作業。
{% asset_img "2.png" %}


### 總結
本人習慣直接操作SQL語言，但LINQ說實在也不錯(可以直接防SQL INJECTION、強型別的優勢)。
但感覺上LINQ比較適合簡單的查詢，變成在程式裏面爬出來後去操作，性能可能會低，但比較穩。
而對於複雜的SQL查詢還是有困難QQ，變成翻譯家的感覺。
基於以上原因，複雜查詢我應該會使用Dapper來做，寫原生。

此外，以有寫過Laravel跟Java的經驗來說，LINQ是C#微軟Only。
也就表示熟悉LINQ可能會有換其他語言SQL卡住、忘記正常該怎麼組的問題。

在這篇中先小試一下，下篇將注重一些平常可能會做的LINQ查詢操作。


**參考資料**
https://docs.microsoft.com/zh-tw/dotnet/csharp/programming-guide/concepts/linq/
https://ithelp.ithome.com.tw/articles/10194468
https://blog.darkthread.net/blog/linq-or-direct-sql/
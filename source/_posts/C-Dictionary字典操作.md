---
title: C# Dictionary字典操作
date: 2021-09-03 18:16:52
categories:
  - [Code,C#]
---

這次練習使用C#的字典資料結構。

### Dictionary

字典型別，一個Key對上一個Value，一對一關係。
像是查字典，你查某個單字，會給你他的解釋。

```
using System;
using System.Collections.Generic;

public class Program
{
	
	public static void Main()
	{
		Dictionary<string, string> dictionary = new Dictionary<string, string>( ); // 建立字典key:string,value:string
		dictionary.Add("LastName","Lin");
		dictionary.Add("FirstName","Yu-Hsiang");
			
		// 從字典中取出對應值
		Console.WriteLine("FirstName:" + dictionary["FirstName"]);
		Console.WriteLine("LastName:" + dictionary["LastName"]);
		
		// 遍歷字典
		foreach ( var item in dictionary )
		{
			Console.WriteLine( "Key = " + item.Key + ", Value = " + item.Value );
		}
	}
}
```
輸出
```
FirstName:Yu-Hsiang
LastName:Lin
Key = LastName, Value = Lin
Key = FirstName, Value = Yu-Hsiang
```
### 結語

有一對一關係的資料，可以利用字典去整理，就能不用二維陣列去做，資料也比較清楚。
如果有重複的Key值，在添加時也會跳出例外事件。例:An item with the same key has already been added.

**參考資料**
http://code2study.blogspot.com/2012/01/c-dictionary.html
https://ithelp.ithome.com.tw/articles/10234391
https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2?view=net-5.0
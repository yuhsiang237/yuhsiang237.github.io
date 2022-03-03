---
title: C# nameof用法
date: 2022-02-09 14:19:50
categories:
  - [Code,C#]
---
如果在C#中遇到一些重複的String字串變數，是由變數的命名而來，就可以使用nameof直接提取出自串，避免再打一次造成打錯的問題。

### nameof
nameof運算式會產生變數、類型或成員的名稱做為字串常數

```csharp
Console.WriteLine(nameof(System.Collections.Generic));  // output: Generic
Console.WriteLine(nameof(List<int>));  // output: List
Console.WriteLine(nameof(List<int>.Count));  // output: Count
Console.WriteLine(nameof(List<int>.Add));  // output: Add

var numbers = new List<int> { 1, 2, 3 };
Console.WriteLine(nameof(numbers));  // output: numbers
Console.WriteLine(nameof(numbers.Count));  // output: Count
Console.WriteLine(nameof(numbers.Add));  // output: Add
```

### 參考資料:
https://docs.microsoft.com/zh-tw/dotnet/csharp/language-reference/operators/nameof#code-try-0
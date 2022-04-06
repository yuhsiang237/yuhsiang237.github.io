---
title: 'C# Tuple實名、匿名用法'
date: 2022-04-06 10:25:27
categories:
  - [Code,C#] 
---
最近在寫資料時需要回傳多筆數，但在資料單純、沒特別的邏輯的情況下不想再建立一個Model回傳，這時就可以使用Tuple返回元組類型。

以下為匿名、實名的兩種Tuple寫法。

```csharp
using System;

namespace TupleSample
{
    internal class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine(String.Concat(GetUser1().Id, GetUser1().Name));
            Console.WriteLine(String.Concat(GetUser2().Item1, GetUser2().Item2));
        }

        static (int Id, string Name) GetUser1()
        {
            return (5, "王小明");
        }


        static Tuple<int, string> GetUser2()
        {
            return Tuple.Create(5, "王小明");
        }
    }
}
```

**output**
```
5王小明
5王小明
```

### 總結
當返回值較為單純時可以使用，如2~3個返回值。

### 參考資料
https://docs.microsoft.com/zh-tw/dotnet/csharp/language-reference/builtin-types/value-tuples
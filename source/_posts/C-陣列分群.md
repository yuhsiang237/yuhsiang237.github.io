---
title: C# 資料分群
date: 2022-05-20 16:48:57
categories:
  - [Code,C#]
---

在這邊主要介紹List的分群，如有以下的五筆資料
["00001","00002","00003","00004","00005"]
這時想要以2來群分，變成下方結果
[["00001","00002"],["00003","00004"],["00005"]]

程式碼:
```csharp
using System;
using System.Linq;
using System.Collections.Generic;
using System.Text.Json;

namespace ListGroupBy
{
    internal class Program
    {
        static void Main(string[] args)
        {
            var numbers = new List<string>
            {
                "00001","00002","00003","00004","00005"
            };

            var numberGroup = numbers
                .Select((number, index) => new { number, index })
                .GroupBy(g => g.index / 2, i => i.number)
                .ToList();
        }
    }
}

```

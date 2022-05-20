---
title: C# 資料分群
date: 2022-05-20 16:48:57
categories:
  - [Code,C#]
---

在這邊主要介紹List的分群，如有以下的五筆資料
```
["00001","00002","00003","00004","00005"]
```
這時想要以2來群分，變成下方結果
```
[["00001","00002"],["00003","00004"],["00005"]]
```

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

            //It will iterate through each groups
            foreach (var group in numberGroup)
            {
                Console.WriteLine($"{group.Key} ,  count: {group.Count()}");
                //Iterate through each number of a group
                foreach (var number in group)
                {
                    Console.WriteLine($"  number : {number}");
                }
            }
        }
    }
}
```

output:
```
0 ,  count: 2
  number : 00001
  number : 00002
1 ,  count: 2
  number : 00003
  number : 00004
2 ,  count: 1
  number : 00005
```

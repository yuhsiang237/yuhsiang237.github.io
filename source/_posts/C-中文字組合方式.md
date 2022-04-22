---
title: C# 文字組合的4種方式
date: 2022-04-22 16:40:22
categories:
  - [Code,C#] 
---
### 前言
在此紀錄C#中常見的組字串方式。

### 實作
```csharp
using System;

namespace StringSol
{
    internal class Program
    {
        static void Main(string[] args)
        {
            var name = "Yu Hsiang";
            string str1 = "你好" + name;
            string str2 = String.Concat("你好", name);
            string str3 = String.Format("你好{0}", name);
            // $是c# 6.0 的語言特性，功能類似string.format()
            string str4 = $"你好{name}";

            Console.WriteLine(str1);
            Console.WriteLine(str2);
            Console.WriteLine(str3);
            Console.WriteLine(str4);
        }
    }
}
```
輸出結果:
```
你好Yu Hsiang
你好Yu Hsiang
你好Yu Hsiang
你好Yu Hsiang
```

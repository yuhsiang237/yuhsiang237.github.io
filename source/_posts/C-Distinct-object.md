---
title: C# Distinct objects
date: 2023-02-14 16:13:37
categories:  
  - [Code,C#]
---
In the case, I want to remove the duplicate objects of the list.
I use the object key, <code>UserId</code> and <code>Type</code>, to distinguish them in the list.
After I distinct the objects, the count of the list becomes 2.
The numbers of the list are 2023021403 and 2023021401.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

namespace App
{
    internal class Program
    {
        private class OrderModel
        {
            public int UserId { get; set; }
            public int Type { get; set; }
            public string Number { get; set; }
        }

        static void Main(string[] args)
        {
            var orders = new List<OrderModel>
            {
                new OrderModel
                {
                    UserId = 1,
                    Type = 1,
                    Number = "2023021401"
                },
                new OrderModel
                {
                    UserId = 1,
                    Type = 1,
                    Number = "2023021402"
                }
                ,
                new OrderModel
                {
                    UserId = 1,
                    Type = 2,
                    Number = "2023021403"
                }
            };

            var distinctList = orders
                               .GroupBy(x => new { x.Type, x.UserId })
                               .Select(x => x.FirstOrDefault())
                               .ToList();

            foreach (var o in distinctList)
            {
                Console.WriteLine($"Number:{o.Number}");
                Console.WriteLine($"UserId:{o.UserId}");
                Console.WriteLine($"Type:{o.Type}");
                Console.WriteLine("===");
            }
        }
    }
}
```

Output:
```
Number:2023021401
UserId:1
Type:1
===
Number:2023021403
UserId:1
Type:2
===
```
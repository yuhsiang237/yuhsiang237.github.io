---
title: C# Flatten data by Linq
date: 2023-05-12 16:09:52
categories:  
  - [Code,C#]
---
When I work on the project, I usually run into a problem about flatting data.
Because the original data could in the nested property, I have to get it out.
The following is an example about nested properties, and the requirement is that I must get all OrderDetailNumber.

Finally, I found an easy method that is use SelectMany.

**LINQ SelectMany**

Projects each element of a sequence to an IEnumerable<T> and flattens the resulting sequences into one sequence.



```csharp
using System;
using System.Linq;
using System.Collections.Generic;

namespace FlattenResult
{
    internal class Program
    {
        public class Order
        {
            public string OrderNumber { get; set; }

            public List<OrderDetail> orderDetails { get; set; }

            public class OrderDetail
            {
                public string OrderDetailNumber { get; set; }
            }
        }

        static void Main(string[] args)
        {
            var orders = new List<Order>
            {
                new Order
                {
                    OrderNumber = "202305121111",
                    orderDetails = new List<Order.OrderDetail>
                        {
                            new Order.OrderDetail
                            {
                                OrderDetailNumber = "OD0001",
                            },
                            new Order.OrderDetail
                            {
                                OrderDetailNumber = "OD0001",
                            }
                        }
                },
                new Order
                {
                    OrderNumber = "202305121112",
                    orderDetails = new List<Order.OrderDetail>
                        {
                            new Order.OrderDetail
                            {
                                OrderDetailNumber = "OD0003",
                            },
                            new Order.OrderDetail
                            {
                                OrderDetailNumber = "OD0004",
                            }
                        }
                }
            };

            var orderDetailNumbers = orders.SelectMany(s => s.orderDetails)
                                           .Select(s => s.OrderDetailNumber)
                                           .ToList();

            orderDetailNumbers.ForEach(f =>
            {
                Console.WriteLine(f);
            });
        }
    }
}

```
Output:
```
OD0001
OD0001
OD0003
OD0004
```
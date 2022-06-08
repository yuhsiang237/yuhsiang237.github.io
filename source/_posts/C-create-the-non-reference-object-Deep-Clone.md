---
title: C# create the non-reference object (Deep Clone)
date: 2022-06-08 10:22:05
categories:
  - [Code,C#]
---
In my work, I usually have a problem about how to create a non-reference object in C#. So today I want to solve it with a simple way that use the serialization and deserialization of JSON.

### Code
```csharp
using System;
using System.Text.Json;

namespace NonReferenceObj
{
    internal class Program
    {
        /// <summary>
        /// Order
        /// </summary>
        public class Order
        {
            public int Seq { get; set; }
            public string No { get; set; }
            public decimal Amount { get; set; }
        }

        /// <summary>
        /// Util
        /// </summary>
        public class Util
        {
            /// <summary>
            /// Deep Clone
            /// Copy a non-reference object
            /// </summary>
            /// <typeparam name="T">T</typeparam>
            /// <param name="obj">obj</param>
            /// <returns></returns>
            public static T DeepClone<T>(T obj)
            => JsonSerializer.Deserialize<T>(JsonSerializer.Serialize(obj));
        }

        static void Main()
        {
            var order = new Order
            {
                Seq = 1,
                No = "20220608",
                Amount = 100,
            };

            var copy = Util.DeepClone(order);

            // Check the reference of object 
            Console.WriteLine($"Check the reference of object: {object.ReferenceEquals(order, copy)}");
            // Check the equal of value  
            Console.WriteLine($"Check the equal of value: {order.No == copy.No && order.Seq == copy.Seq && order.Amount == copy.Amount}");
        }
    }
}
```

Finally, we got the output below:

```
Check the reference of object: False
Check the equal of value: True
```


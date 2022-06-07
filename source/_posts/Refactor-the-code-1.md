---
title: Refactor the code - function
date: 2022-06-07 14:34:00
categories:
  - [Code,C#]
  - [Code,Refactoring]
---

First, we find a section of code that contains different operations, such as initialization and print. We can try to refactor that extracts them into functions.

**Concepts**
1. The process of restructuring code, while not changing its original functionality.
2. Easier to understand
3. Cheaper to modify

```csharp
using System;
using System.Collections.Generic;

namespace refactor01
{
    internal class Program
    {
        static void Main(string[] args)
        {
            var numbers = new List<string>();
            for(var i = 0; i < 10; i++)
            {
                numbers.Add(i.ToString());
            }
            foreach (var item in numbers)
            {
                Console.Write(item);
            }
        }
    }
}
```

Now, We extract the different operations into functions, and each function just do one thing. We can more easily control something we want to do.

1. Extract the different operations into functions.
2. Add the summary.
3. Remove the unused parameter.

```csharp
using System;
using System.Collections.Generic;

namespace refactor01
{
    internal class Program
    {
        static void Main()
        {
            var numbers = new List<string>();
            initNumbers(numbers);
            printNumbers(numbers);
        }

        /// <summary>
        /// init the numbers
        /// </summary>
        /// <param name="numbers">list of numbers</param>
        public static void initNumbers(List<string> numbers)
        {
            for (var i = 0; i < 10; i++)
            {
                numbers.Add(i.ToString());
            }
        }

        /// <summary>
        /// print the numbers
        /// </summary>
        /// <param name="numbers">list of numbers</param>
        public static void printNumbers(List<string> numbers)
        {
            foreach (var item in numbers)
            {
                Console.Write(item);
            }
        }
    }
}
```

### In conclusion
Finally, we did it that finished the refactoring and just made it easy to maintain and read.

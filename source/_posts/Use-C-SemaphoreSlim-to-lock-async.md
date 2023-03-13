---
title: Use C# SemaphoreSlim to lock async functions
date: 2023-03-13 17:18:49
categories:
  - [Topic,2022 C# SyncAndAsync]
  - [Code,C#] 
---
When writing an async function or method, I encountered a problem with accessing data one-by-one, such as starting 100000 async APIs, which could lead to bugs. 

Below is an problem example. I expect the result to be "Sum:100000", but the actual result is "Sum:99995" or other abnormal results.

**Example 1:**
```csharp
namespace SemaphoreSlimExample;

public class Program
{
    private static int _sum;

    public  static void Main(string[] args)
    {
        Task.WaitAll(Enumerable
            .Range(0, 100000)
            .Select(x => Add(1))
            .ToArray());
        Console.WriteLine($"Sum:{_sum}");
    }

    private static async Task Add(int num)
    {
        await Task.Run(() =>
        {
            _sum += num;
        });
    }
}

```
**Result:**
```
Sum:99995
```


To fix this problem, I added the SemaphoreSlim to code, and the result always is correct  "Sum:100000".
It helps us lock and limit the asynchronous function's data to one-by-one. 

What is Semaphoreslim:
https://learn.microsoft.com/en-us/dotnet/api/system.threading.semaphoreslim?view=net-7.0

Represents a lightweight alternative to Semaphore that limits the number of threads that can access a resource or pool of resources concurrently.


**Example2:**
```csharp
namespace SemaphoreSlimExample;

public class Program
{
    private static int _sum;
    private static readonly SemaphoreSlim locker = new SemaphoreSlim(1, 1);

    public static void Main(string[] args)
    {
        Task.WaitAll(Enumerable
            .Range(0, 100000)
            .Select(x => Add(1))
            .ToArray());
        Console.WriteLine($"Sum:{_sum}");
    }

    private static async Task Add(int num)
    {
        await locker.WaitAsync();

        await Task.Run(() =>
        {
            _sum += num;
        });

        locker.Release();
    }
}

```
**Result:**
```
Sum:100000
```

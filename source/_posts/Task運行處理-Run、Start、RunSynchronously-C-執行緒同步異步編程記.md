---
title: '[Day6]Task運行處理(Run、Start、RunSynchronously)'
date: 2022-03-20 16:27:53
categories:
  - [Topic,2022 C# SyncAndAsync]
  - [Code,C#] 
---
### 前言
在上回 {% post_link 'Task等待處理-Wait、WaitAll、WaitAny、WaitAsync、WhenAll、WhenAny-C-SyncAndAsync' '[Day5]Task等待處理(Wait、WaitAll、WaitAny、WaitAsync、WhenAll、WhenAny) - C# SyncAndAsync' %} ，我們使用了Task來測試等待的效果，而這回主要探討的是執行運行。

- Task.Run:將指定在 ThreadPool 執行工作排入佇列，並傳回該工作的工作或 Task<TResult> 控制代碼。
- Task.Start:啟動 Task，並排定它在目前的 TaskScheduler 執行。
- Task.RunSynchronously:在目前的 Task 上同步執行 TaskScheduler。

### 讓一個Task運行的兩種做法
其實Task.Run、Task.Start都是能達到一樣的結果，只是寫法一個是分兩段，一個是一氣呵成。

1.第一種用new Task搭配Task.Start，必須寫兩段。好處是可以先建立了，再決定哪時啟用。
```csharp
public static void Main(string[] args)
{
    Task task = new Task(() => Console.WriteLine("task"));
    task.Start();
}
```

2.第二種用Run，一氣呵成。執行當下就直接運行。
```csharp
public static void Main(string[] args)
{
    Task Task = Task.Run(() =>
            {
                Console.WriteLine("task");
            });
}
```

因此以上兩種用法Task.Run、Task.Start的差別已經清楚了。
再來則是RunSynchronously用法。

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

public class Example
{
    public static void Main()
    {
        Console.WriteLine("Application executing on thread {0}",
                          Thread.CurrentThread.ManagedThreadId);
        var asyncTask = Task.Run(() => {
            Console.WriteLine("Task {0} (asyncTask) executing on Thread {1}",
                              Task.CurrentId,
                              Thread.CurrentThread.ManagedThreadId);
            long sum = 0;
            for (int ctr = 1; ctr <= 1000000; ctr++)
                sum += ctr;
            return sum;
        });
        var syncTask = new Task<long>(() => {
            Console.WriteLine("Task {0} (syncTask) executing on Thread {1}",
                               Task.CurrentId,
                               Thread.CurrentThread.ManagedThreadId);
            long sum = 0;
            for (int ctr = 1; ctr <= 1000000; ctr++)
                sum += ctr;
            return sum;
        });
        syncTask.RunSynchronously();
        Console.WriteLine();
        Console.WriteLine("Task {0} returned {1:N0}", syncTask.Id, syncTask.Result);
        Console.WriteLine("Task {0} returned {1:N0}", asyncTask.Id, asyncTask.Result);
    }
}
```
執行結果:
```
Application executing on thread 1
Task 2 (syncTask) executing on Thread 1
Task 1 (asyncTask) executing on Thread 4

Task 2 returned 500,000,500,000
Task 1 returned 500,000,500,000
```
官方給的解釋:
在這兩種情況下，工作都會執行相同的 lambda 運算式，以顯示工作識別碼和工作執行所在之執行緒的識別碼。此工作會計算介於1到1000000之間的整數總和。 如範例的輸出所示，藉由呼叫方法來執行的工作 RunSynchronously 會在應用程式執行緒上執行，而非同步工作則不是。

簡單來說就是:RunSynchronously會在主執行序上面跑。而非同步的則會在其他執行序。

### 總結
在這回中可以知道讓Task執行有哪幾種作法。

### 參考資料
- https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.task.run?view=net-6.0
- https://www.itread01.com/content/1569240242.html
- https://docs.microsoft.com/zh-tw/dotnet/api/system.threading.tasks.task.runsynchronously?view=net-6.0
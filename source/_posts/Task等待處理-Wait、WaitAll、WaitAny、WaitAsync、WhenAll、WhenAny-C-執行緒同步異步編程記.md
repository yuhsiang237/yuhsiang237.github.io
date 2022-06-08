---
title: '[Day5]Task等待處理(Wait、WaitAll、WaitAny、WaitAsync、WhenAll、WhenAny) - C# SyncAndAsync'
date: 2022-03-20 16:26:53
categories:
  - [Topic,2022 C# SyncAndAsync]
  - [Code,C#] 
---
### 前言
在上回 {% post_link '使用Task撰寫第一個非同步程式-C-SyncAndAsync' '[Day4]使用Task撰寫第一個非同步程式 - C# SyncAndAsync' %} ，我們使用了Task來撰寫第一隻非同步的程式。
而在這回將會著重在等待方面。

Task提供以下語法能夠等待:Wait、WaitAll、WaitAny、WaitAsync、WhenAll、WhenAny。

- Task.Wait:等候 Task 完成執行。
- Task.WaitAll:等待所有提供的 Task 物件完成執行。
- Task.WaitAny:等候任一提供的 Task 物件完成執行。
- Task.WaitAsync:取得 Task 當此 Task 完成或指定的超時時間過期時將完成的。(ps.是.NET6, 7 Preview 1才支援的新語法，目前是用.net core 3.1所以先不做範例)
- Task.WhenAll:建立當所有提供的工作完成時才會完成的工作。
- Task.WhenAny:建立當任何一個提供的工作完成時才會完成的工作。

這麼看來，使用Wait、When差別好像很難分清楚?再稍微比較一下:

<table>
<tr>
<td></td>
<td>Task.WhenAll</td>
<td>Task.WaitAll</td>
</tr>
<tr>
<td>調用時阻塞該線程</td>
<td>不會</td>
<td>會</td>
</tr>
<tr>
<td>返回值</td>
<td>Task</td>
<td>無</td>
</tr>
<tr>
<td>備註</td>
<td>可以用返回的Task去檢查是否完成</td>
<td></td>
</tr>
</table>

此外，Task.WhenAll()也能用前面加await的語法去製造出阻塞。
```csharp
await Task.WhenAll()
```
因此在使用上，能夠全部使用When再搭配await去組合出Wait的效果。

### 實作測試
```csharp
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Threading.Tasks;

namespace TaskWaitHandle
{
    public class Program
    {
        public static void Main(string[] args)
        {
            TaskWaitTest();

            TaskWaitAllTest();
            TaskWhenAllTest();

            TaskWaitAnyTest();
            TaskWhenAnyTest();
        }

        /// <summary>
        /// 測試Task.WaitAny
        /// </summary>
        public static void TaskWaitAnyTest()
        {
            Stopwatch sw = new Stopwatch();
            sw.Start();


            var tasks = new List<Task> { DelayTime(5000), DelayTime(3000), DelayTime(2000) };
            Task.WaitAny(tasks.ToArray());

            string second = (sw.Elapsed.TotalMilliseconds / 1000).ToString();
            Console.WriteLine(nameof(TaskWaitAnyTest) + " : 一共花費:" + second + "秒");
        }

        /// <summary>
        /// 測試Task.WhenAny
        /// </summary>
        public static void TaskWhenAnyTest()
        {
            Stopwatch sw = new Stopwatch();
            sw.Start();


            var tasks = new List<Task> { DelayTime(5000), DelayTime(3000), DelayTime(2000) };
            var t = Task.WhenAny(tasks);

            string second = (sw.Elapsed.TotalMilliseconds / 1000).ToString();
            Console.WriteLine(nameof(TaskWhenAnyTest) + " : 一共花費:" + second + "秒");
        }

        /// <summary>
        /// 測試Task.WhenAll
        /// </summary>
        public static void TaskWhenAllTest()
        {
            Stopwatch sw = new Stopwatch();
            sw.Start();


            var tasks = new List<Task> { DelayTime(5000), DelayTime(3000), DelayTime(2000) };
            var t = Task.WhenAll(tasks);

            string second = (sw.Elapsed.TotalMilliseconds / 1000).ToString();
            Console.WriteLine(nameof(TaskWhenAllTest) + " : 一共花費:" + second + "秒");
        }

        /// <summary>
        /// 測試Task.WaitAll
        /// </summary>
        public static void TaskWaitAllTest()
        {
            Stopwatch sw = new Stopwatch();
            sw.Start();


            var tasks = new List<Task> { DelayTime(5000), DelayTime(3000), DelayTime(2000) };
            Task.WaitAll(tasks.ToArray());

            sw.Stop();
            string second = (sw.Elapsed.TotalMilliseconds / 1000).ToString();
            Console.WriteLine(nameof(TaskWaitAllTest) + " : 一共花費:" + second + "秒");
        }

        /// <summary>
        /// 測試Task.Wait
        /// </summary>
        public static void TaskWaitTest()
        {
            Stopwatch sw = new Stopwatch();
            sw.Start();


            Task t = DelayTime(5000);
            t.Wait();
            sw.Stop();


            string second = (sw.Elapsed.TotalMilliseconds / 1000).ToString();
            Console.WriteLine(nameof(TaskWaitTest) + " : 一共花費:" + second + "秒");
        }

        /// <summary>
        /// 停止一段時間
        /// </summary>
        /// <param name="millseconds">停止毫秒</param>
        /// <returns></returns>
        public static async Task DelayTime(int millseconds)
        {
            await Task.Delay(millseconds);
        }
    }
}
```

執行完結果:
```
TaskWaitTest : 一共花費:5.0991762秒
TaskWaitAllTest : 一共花費:5.017742599999999秒
TaskWhenAllTest : 一共花費:0.0008574秒
TaskWaitAnyTest : 一共花費:2.0228913秒
TaskWhenAnyTest : 一共花費:0.0006043秒
```
可以見到When不會阻塞所以直接被帶過，而Wait造成阻塞。


### 總結
可以知道Task使用Wait會阻塞，When不會阻塞。
而使用When可以再之後去檢查該返回值是否完成。

至於如果想讓When阻塞，就使用await即可。
所以總結是:**When搭await可以組出所有阻塞/非阻塞，因此一路組合技用到底即可，而Wait可以擺一邊放置了。**

### 參考資料
- https://docs.microsoft.com/zh-tw/dotnet/api/system.threading.tasks.task.wait?view=net-6.0
- https://csharpkh.blogspot.com/2019/04/CSharp-WhenAll-WaitAll-Async-different.html
- https://stackoverflow.com/questions/6123406/waitall-vs-whenall
- https://www.nhooo.com/note/qa02fb.html








---
title: '[Day4]使用Task撰寫第一個非同步程式 - C# SyncAndAsync'
date: 2022-03-19 16:26:53
categories:
  - [Topic,2022 C# SyncAndAsync]
  - [Code,C#] 
---
### 前言
在上回 {% post_link 'C-中Task與Thread比較-C-SyncAndAsync' '[Day3]C#中Task與Thread比較 - C# SyncAndAsync' %} ，比較了Task、Thread，而這回主要會以Task撰寫第一個非同步程式。


### 什麼是Task
Task類別代表不會傳回值，而且通常會以非同步方式執行的單一作業。 Task物件是工作架構非同步模式的其中一個核心元件，第一次是在 .NET Framework 4 中引進。 由於物件所執行的工作 Task 通常會線上程集區執行緒上以非同步方式執行，而不是在主應用程式執行緒上同步執行，因此您可以使用 Status 屬性以及 IsCanceled 、 IsCompleted 和 IsFaulted 屬性來判斷工作的狀態。 最常見的情況是使用 lambda 運算式來指定工作要執行的工作。

針對傳回值的作業，您可以使用 Task＜TResult＞ 類別。


### 需求
我們有五件事情分別如下:
1. 起床
2. 打遊戲
3. 聽音樂
4. 吃零食
5. 睡覺

而我們預計起床->同時執行打遊戲、聽音樂、吃零食->睡覺。
整體的順序會如下圖:
{% asset_img "1.png" %}

### 程式撰寫
再來我們快速撰寫一下程式碼。

我們將以下的事情都設定一個執行的時間:
1. 起床，2秒
2. 打遊戲，5秒
3. 聽音樂，4秒
4. 吃零食，4秒
5. 睡覺，3秒

為了要使程式能夠等待，因此我們在函式上需要加入async，才能再裡面寫await。(不然只能用後面加上.Wait()的作法，有點鱉就是)

我在前後都加上了Stopwatch碼表，用來計時，如果照理論，那完成時間是10秒。
因為2~4項是併發異步執行只要取最高的打遊戲。
即:2+5+3=10

在每個事情裡面使用            
await Task.Delay(5000);
去設定每件事情做的時間

21~24行則呼叫事情，並且把它加入List。
利用await Task.WhenAll去等待所有List中Task執行完畢後才執行接下來的事情。

程式碼放置庫:https://github.com/yuhsiang237/TaskAsync

```csharp
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Threading.Tasks;

namespace TaskAsync
{
    public class Program
    {
        /// <summary>
        /// 主程式
        /// </summary>
        /// <returns></returns>
        public static async Task Main()
        {
            Stopwatch sw = new Stopwatch();
            sw.Start();

            await WakeUpAsync();

            IList<Task> tasks = new List<Task>();
            tasks.Add(PlayGameAsync());
            tasks.Add(ListenMusicAsync());
            tasks.Add(EatSnackAsync());

            await Task.WhenAll(tasks);

            await GoToBedSleepAsync();

            sw.Stop();
            string second = (sw.Elapsed.TotalMilliseconds / 1000).ToString();
            Console.WriteLine("一共花費:" + second + "秒");
        }

        /// <summary>
        /// 模擬打遊戲
        /// </summary>
        /// <returns></returns>
        private static async Task PlayGameAsync()
        {
            Console.WriteLine("打遊戲");
            await Task.Delay(5000);
        }

        /// <summary>
        /// 模擬聽音樂
        /// </summary>
        /// <returns></returns>
        private static async Task ListenMusicAsync()
        {
            Console.WriteLine("聽音樂");
            await Task.Delay(4000);
        }

        /// <summary>
        /// 模擬吃零食
        /// </summary>
        /// <returns></returns>
        private static async Task EatSnackAsync()
        {
            Console.WriteLine("吃零食");
            await Task.Delay(4000);
        }

        /// <summary>
        /// 模擬起床
        /// </summary>
        /// <returns></returns>
        private static async Task WakeUpAsync()
        {
            Console.WriteLine("起床");
            await Task.Delay(2000);
        }

        /// <summary>
        /// 模擬睡覺
        /// </summary>
        /// <returns></returns>
        private static async Task GoToBedSleepAsync()
        {
            Console.WriteLine("睡覺");
            await Task.Delay(3000);
        }
    }
}
```
執行結果:
```
起床
打遊戲
聽音樂
吃零食
睡覺
一共花費:10.1614089秒
```
運行:
{% asset_img "2.gif" %}

### 總結
使用Task去處理同步、異步上手挺容易的!
然比較需注意的點在如果你要在裡面用await，外層一定要加上async，否則會報錯。

程式碼放置庫:https://github.com/yuhsiang237/TaskAsync

### 參考資料
- https://docs.microsoft.com/zh-tw/dotnet/api/system.threading.tasks.task?view=net-6.0
- https://docs.microsoft.com/zh-tw/dotnet/api/system.threading.tasks.task.-ctor?view=net-6.0
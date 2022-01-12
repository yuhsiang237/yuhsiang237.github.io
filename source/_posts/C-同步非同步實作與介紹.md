---
title: C#同步非同步實作與介紹
date: 2022-01-12 16:24:37
categories:
  - [程式語言,C#]
---
### 前言
在這篇中簡易介紹C#同步與非同步的作法。
一般我們在做一些任務時有時需要:
1.等待上個任務完成
2.同時處理多個任務
這時就會需要用到非同步、同步的作法，可以節省時間(在同個時間內處理多個任務)。
可能用到Thead或是用Task作法<code>async</code>、<code>await</code>。在這篇中則以:
https://docs.microsoft.com/zh-tw/dotnet/csharp/programming-guide/concepts/async/ 
作為範例，簡易的操作一個需要處理同步/非同步的範例。


### 同步範例實作
簡單來說，就是每個任務都是會等待上面的任務完成才依序執行。
下方以製作一份早餐的同步範例，1~7步驟都要等上面的步驟完成才會繼續。
{% asset_img "synchronous-breakfast.png" %}

```
using System;
using System.Threading.Tasks;

namespace async
{
    class Program
    {
        static void Main(string[] args)
        {
            Coffee cup = PourCoffee();
            Console.WriteLine("coffee is ready");

            Egg eggs = FryEggs(2);
            Console.WriteLine("eggs are ready");

            Bacon bacon = FryBacon(3);
            Console.WriteLine("bacon is ready");

            Toast toast = ToastBread(2);
            ApplyButter(toast);
            ApplyJam(toast);
            Console.WriteLine("toast is ready");

            Juice oj = PourOJ();
            Console.WriteLine("oj is ready");
            Console.WriteLine("Breakfast is ready!");
        }

        private static Juice PourOJ()
        {
            Console.WriteLine("Pouring orange juice");
            return new Juice();
        }

        private static void ApplyJam(Toast toast) =>
            Console.WriteLine("Putting jam on the toast");

        private static void ApplyButter(Toast toast) =>
            Console.WriteLine("Putting butter on the toast");

        private static Toast ToastBread(int slices)
        {
            for (int slice = 0; slice < slices; slice++)
            {
                Console.WriteLine("Putting a slice of bread in the toaster");
            }
            Console.WriteLine("Start toasting...");
            Task.Delay(3000).Wait();
            Console.WriteLine("Remove toast from toaster");

            return new Toast();
        }

        private static Bacon FryBacon(int slices)
        {
            Console.WriteLine($"putting {slices} slices of bacon in the pan");
            Console.WriteLine("cooking first side of bacon...");
            Task.Delay(3000).Wait();
            for (int slice = 0; slice < slices; slice++)
            {
                Console.WriteLine("flipping a slice of bacon");
            }
            Console.WriteLine("cooking the second side of bacon...");
            Task.Delay(3000).Wait();
            Console.WriteLine("Put bacon on plate");

            return new Bacon();
        }

        private static Egg FryEggs(int howMany)
        {
            Console.WriteLine("Warming the egg pan...");
            Task.Delay(3000).Wait();
            Console.WriteLine($"cracking {howMany} eggs");
            Console.WriteLine("cooking the eggs ...");
            Task.Delay(3000).Wait();
            Console.WriteLine("Put eggs on plate");

            return new Egg();
        }

        private static Coffee PourCoffee()
        {
            Console.WriteLine("Pouring coffee");
            return new Coffee();
        }

        private class Coffee
        {
        }

        private class Egg
        {
        }

        private class Toast
        {
        }

        private class Juice
        {
        }

        private class Bacon
        {

        }
    }
}
```
結果：
{% asset_img "1.gif" %}
可以發現這樣很難節省時間，所以其實能把某些部分拆成異步來做，有助於節省時間。(雖然同時間可能記憶體耗能會加大)

### 異步範例實作
準備早餐程式碼的簡單非同步版本看起來像下列程式碼片段：
FryEggsAsync、FryBaconAsync、MakeToastWithButterAndJamAsync更新為Task，函式使用<code>Async</code>。
這時會變成先做第一步，之後2、3、(4+5)步是併發執行，並使用while做判斷，依序把完成的Task移除List，仍需處理的Task數量為0就跳出，代表完成了!
再由下個任務6繼續。

{% asset_img "whenany-async-breakfast.png" %}


```
using System;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace async
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Coffee cup = PourCoffee();
            Console.WriteLine("coffee is ready");

            var eggsTask = FryEggsAsync(2);
            var baconTask = FryBaconAsync(3);
            var toastTask = MakeToastWithButterAndJamAsync(2);

            var breakfastTasks = new List<Task> { eggsTask, baconTask, toastTask };
            while (breakfastTasks.Count > 0)
            {
                Task finishedTask = await Task.WhenAny(breakfastTasks);
                if (finishedTask == eggsTask)
                {
                    Console.WriteLine("eggs are ready");
                }
                else if (finishedTask == baconTask)
                {
                    Console.WriteLine("bacon is ready");
                }
                else if (finishedTask == toastTask)
                {
                    Console.WriteLine("toast is ready");
                }
                breakfastTasks.Remove(finishedTask);
            }

            Juice oj = PourOJ();
            Console.WriteLine("oj is ready");
            Console.WriteLine("Breakfast is ready!");
        }
        static async Task<Toast> MakeToastWithButterAndJamAsync(int number)
        {
            var toast = await ToastBreadAsync(number);
            ApplyButter(toast);
            ApplyJam(toast);

            return toast;
        }

        private static Juice PourOJ()
        {
            Console.WriteLine("Pouring orange juice");
            return new Juice();
        }

        private static void ApplyJam(Toast toast) =>
            Console.WriteLine("Putting jam on the toast");

        private static void ApplyButter(Toast toast) =>
            Console.WriteLine("Putting butter on the toast");

        private static Toast ToastBread(int slices)
        {
            for (int slice = 0; slice < slices; slice++)
            {
                Console.WriteLine("Putting a slice of bread in the toaster");
            }
            Console.WriteLine("Start toasting...");
            Task.Delay(3000).Wait();
            Console.WriteLine("Remove toast from toaster");

            return new Toast();
        }

        private static async Task<Bacon> FryBaconAsync(int slices)
        {
            Console.WriteLine($"putting {slices} slices of bacon in the pan");
            Console.WriteLine("cooking first side of bacon...");
            await Task.Delay(3000);
            for (int slice = 0; slice < slices; slice++)
            {
                Console.WriteLine("flipping a slice of bacon");
            }
            Console.WriteLine("cooking the second side of bacon...");
            await Task.Delay(3000);
            Console.WriteLine("Put bacon on plate");

            return new Bacon();
        }


        private static async Task<Egg> FryEggsAsync(int howMany)
        {
            Console.WriteLine("Warming the egg pan...");
            await Task.Delay(3000);
            Console.WriteLine($"cracking {howMany} eggs");
            Console.WriteLine("cooking the eggs ...");
            await Task.Delay(3000);
            Console.WriteLine("Put eggs on plate");

            return new Egg();
        }

        private static Coffee PourCoffee()
        {
            Console.WriteLine("Pouring coffee");
            return new Coffee();
        }
        private static async Task<Toast> ToastBreadAsync(int slices)
        {
            for (int slice = 0; slice < slices; slice++)
            {
                Console.WriteLine("Putting a slice of bread in the toaster");
            }
            Console.WriteLine("Start toasting...");
            await Task.Delay(3000);
            Console.WriteLine("Remove toast from toaster");

            return new Toast();
        }
        private class Coffee
        {
        }

        private class Egg
        {
        }

        private class Toast
        {
        }

        private class Juice
        {
        }

        private class Bacon
        {

        }
    }
}

```
結果:
可以發現為異步執行!
FryEggsAsync:原本需6秒
FryBaconAsync:原本需6秒
MakeToastWithButterAndJamAsync:原本需3秒
以同步執行的話需要:15秒
在異步執行只需取其中最大的秒數:6秒
大幅度的減省時間花費。


{% asset_img "2.gif" %}
用紅筆畫起來部分，表示其併發執行。
{% asset_img "3.PNG" %}

### 結論
以Async為開頭的函數內步才能夠進行併發的操作。
並且搭配可以搭配await做Task的停頓。
即讓每個可Async的Task充分利用時間。

基本上這樣就能處理掉很多功能了，如果要再深入探討可以參考:
https://docs.microsoft.com/zh-tw/dotnet/csharp/async


### 參考資料
https://docs.microsoft.com/zh-tw/dotnet/csharp/programming-guide/concepts/async/


---
title: '[Day7]C#建立不會停止的背景Task'
date: 2022-04-08 09:43:59
categories:
  - [Topic,2022 C# 執行緒同步異步編程記]
  - [Code,C#] 
---
### 前言
在上回 {% post_link 'Task運行處理-Run、Start、RunSynchronously-C-執行緒同步異步編程記' '[Day6]Task運行處理(Run、Start、RunSynchronously)
' %} ，我們學習了Task的各種啟動方式。
而今回主要來撰寫不會停止的背景Task。

### 實作
不終止的非同步事件 ex:多個while(true)常駐。

主要使用以下的模板去製作常駐程式:
```csharp
var endlessTask = Task.Run(async () =>
    {
        while (true)
        {
          // Todo
        }
    });
```
簡單來說就是開一個異步的Task讓他跑即可。

在以下的範例中endlessTask1、endlessTask2會不停的印出資料。
```csharp
using System;
using System.Threading.Tasks;

namespace EndlessTaskAsync
{
    class Program
    {
        /// <summary>
        /// 主程式
        /// </summary>
        /// <returns></returns>
        public static async Task Main()
        {
            var endlessTask1 = EndlessTask(1);
            var endlessTask2 = EndlessTask(2);

            await PreventToStop();
        }

        /// <summary>
        /// 製作無止盡的 while task
        /// </summary>
        /// <param name="number">編號</param>
        /// <returns></returns>
        private static Task EndlessTask(int number)
        {
            Console.WriteLine("Start EndlessTask" + number);
            var endlessTask = Task.Run(async () =>
            {
                while (true)
                {
                    Console.WriteLine("EndlessTask" + number);
                    await Task.Delay(100);
                }
            });
            return Task.CompletedTask;
        }

        /// <summary>
        /// 避免視窗關閉，使用ReadKey();
        /// </summary>
        /// <returns></returns>
        private static Task PreventToStop()
        {
            Console.ReadKey();
            return Task.CompletedTask;
        }
    }
}
```
運行結果:
```
EndlessTask1
EndlessTask2
EndlessTask1
EndlessTask2
EndlessTask1
EndlessTask2
EndlessTask1
EndlessTask2
EndlessTask1
EndlessTask2
EndlessTask1
EndlessTask2
EndlessTask1
EndlessTask2
EndlessTask1
EndlessTask2
EndlessTask1
EndlessTask2
```
直到關掉程式都會一直印。

### 總結
在這回中練習了背景常駐的Task，通常會在監聽某個請求while中使用。
如果是以目前處理過的code，就是在socket處理上會大量使用。

專案存庫:
https://github.com/yuhsiang237/EndlessTaskAsync

---
title: '[Day3]C#中Task與Thread比較 - C# SyncAndAsync'
date: 2022-03-19 15:47:05
categories:
  - [Topic,2022 C# SyncAndAsync]
  - [Code,C#] 
---
### 前言
在上回 {% post_link '什麼是同步與異步-C-SyncAndAsync' '[Day2]什麼是同步與異步 - C# SyncAndAsync' %} ，列出了同步異步差異，而今回就朝異步的Task、Thread邁進。

### 什麼是Task與Thread
在此將Task、Thread做一個表格比較:
<table>
<tr>
<td style="width:20%"></td>
<td style="width:40%">Task</td>
<td style="width:40%">Thread</td>
</tr>
<tr>
<td>描述</td>
<td>Task類別代表不會傳回值，而且通常會以非同步方式執行的單一作業。 Task物件是工作架構非同步模式的其中一個核心元件，第一次是在 .NET Framework 4 中引進。 由於物件所執行的工作 Task 通常會線上程集區執行緒上以非同步方式執行，而不是在主應用程式執行緒上同步執行，因此您可以使用 Status 屬性以及 IsCanceled 、 IsCompleted 和 IsFaulted 屬性來判斷工作的狀態。 最常見的情況是使用 lambda 運算式來指定工作要執行的工作。

針對傳回值的作業，您可以使用Task＜TResult＞類別。</td>
<td>可建立和控制執行緒，設定執行緒的優先權，並取得它的狀態。
Thread是C#中最早的多執行緒模型，後來才推出Task。
</td>
</tr>
<tr>
<td>基於</td>
<td>基於Action,Func的更加現代的執行緒模型。支援模板引數。</td>
<td>基於delegate。僅受限於固定引數。</td>
</tr>
<tr>
<td>C#中常配套的關鍵詞</td>
<td>async、await</td>
<td>Start、Suspend、Resume、Join、Abort、ThreadPool</td>
</tr>
<tr>
<td>namespace</td>
<td>System.Threading.Tasks</td>
<td>System.Threading</td>
</tr>
<tr>
<td>備註</td>
<td>Task的目的，就是要替代Thread，未來可能比較主流</td>
<td>-</td>
</tr>

</table>

### 總結
可以得知Task、Thread都是能夠處理異步的解決方案，而Task算是Thread的改良易用的封裝版本，因此接下來會先以Task為主進行深究，最後才是Thread。

而在MSDN官方也提供了一個非同步程式的配套設計模式:
https://docs.microsoft.com/zh-tw/dotnet/standard/asynchronous-programming-patterns/


### 參考資料
- https://docs.microsoft.com/zh-tw/dotnet/api/system.threading.thread?view=net-6.0
- https://docs.microsoft.com/zh-tw/dotnet/api/system.threading.tasks.task?view=net-6.0
- https://www.juduo.cc/club/1064920.html
- https://docs.microsoft.com/zh-tw/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap
- https://docs.microsoft.com/zh-tw/dotnet/standard/asynchronous-programming-patterns/
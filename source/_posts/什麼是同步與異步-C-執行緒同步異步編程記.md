---
title: '[Day2]什麼是同步與異步 - C# SyncAndAsync'
date: 2022-03-19 14:51:24
categories:
  - [Topic,2022 C# SyncAndAsync]
  - [Code,C#] 
---
### 前言
在上回 {% post_link '為了某個夢想而開始-C-同步異步編程' '[Day1]為了某個夢想而開始 - C# SyncAndAsync' %} ，列出了學習方向，而今回就朝學習目標邁進。


### 什麼是同步與異步
在此將同步、非同步做一個表格比較:
<table>
<tr>
<td style="width:20%"></td>
<td style="width:40%">異步</td>
<td style="width:40%">同步</td>
</tr>
<tr>
<td>描述</td>
<td>又稱非同步(Asynchronous、async)，調用在發出之後，這個調用就直接返回了，所以沒有返回結果。當一個異步過程調用發出後，調用者不會立刻得到結果。而是在調用發出後，被調用者通過狀態、通知或通過回調函數，讓調用者能響應結果。</td>
<td>又稱同步(Synchronous、sync)，同步還可以理解為：發出一個調用時，在沒有得到結果之前，該調用就不返回。</td>
</tr>
<tr>
<td>使用場景</td>
<td>同時，一次做多件事情</td>
<td>同時，一次做一件事情</td>
</tr>
<td>圖例</td>
<td>{% asset_img "1.png" %}</td>
<td>{% asset_img "2.png" %}</td>
</tr>
<tr>
<td>例子</td>
<td>非同步串行通信、AJAX</td>
<td>-</td>
</tr>
<tr>
<td>C#中常見的關鍵詞</td>
<td>async、await、Task、Thread </td>
<td>-</td>
</tr>
</table>

### 總結
在這篇中可以得知，如果想加快某些代碼執行的速度，而其彼此又不相干，就能將其以非同步方法實作。
且非同步方法調用後立即返回，因此不會有結果，而是用通知、回調方式去獲得結果。
而在C#中Thread與Task都能做到異步，下回則主要介紹Thread與Task的差異囉。


### 參考資料 
- https://zh.wikipedia.org/wiki/%E9%9D%9E%E5%90%8C%E6%AD%A5
- https://zh.wikipedia.org/wiki/%E5%90%8C%E6%AD%A5
- https://docs.microsoft.com/zh-tw/dotnet/csharp/programming-guide/concepts/async/
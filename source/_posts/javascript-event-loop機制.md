---
title: javascript event loop機制(未完)
date: 2021-08-30 03:40:36
categories:
   - [Code,Javascript]
---

### 前言
javascript是單一執行序，所以一次只能執行一件事。我們網頁上的操作也都會影響這機制，造成效能不好(時間延誤)。
而這其實很難用文字去解釋，我們可以看這支影片。
https://www.youtube.com/watch?v=8aGhZQkoFbQ

{% asset_img 1.PNG Js event loop %}

### 小結

1.盡量不要在 callback 當中執行負擔過重的函數，避免佔據 call stack
2.理解 microtask 與一般 task queue 執行順序不同。
－－引用自參考資料

雖然理解底層跟實際面操作其實並不會有什麼大改變，因為還是得寫。
但至少可以透過這些底層觀念，讓我們思考有些程式碼其實有更好的寫法，就能減少這種效能問題。
~~(雖然使用者本身就只會覺得是網路lag而已)~~

其實還有microtask未補，待下次補完。

**參考資料**
https://ithelp.ithome.com.tw/articles/10214017
https://www.youtube.com/watch?v=8aGhZQkoFbQ


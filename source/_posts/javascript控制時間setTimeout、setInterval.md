---
title: javascript控制時間setTimeout、setInterval
date: 2021-08-29 14:53:02
categories:
  - [程式語言,Javascript]
---
javascript在處理網頁上"連續執行"、"只執行一次"必見到的時間控制函式setTimeout、setInterval。

### setTimeout
延遲某一段時間後才執行一次，每個setTimeout都可以得到一個timer ID。
單位:毫秒
```
var timeoutID = setTimeout(function[, delay, arg1, arg2, ...]);
var timeoutID = setTimeout(function[, delay]);
var timeoutID = setTimeout(code[, delay]);
```

用法:
```
const timeroutID = window.setTimeout((()=>{console.log('Hello world')}),1000);
```

Q:如果要清除setTimeout呢?
利用clearTimeout

用法:
```
const timeroutID = window.setTimeout((()=>{console.log('Hello world')}),1000);
window.clearTimeout(timeroutID);
```

### setInterval
延遲時間後，不斷循環某段程式碼，每個setInterval都可以得到一個interval ID。
單位:毫秒
```
var intervalID = setInterval(func, [delay, arg1, arg2, ...]);
var intervalID = setInterval(function[, delay]);
var intervalID = setInterval(code, [delay]);
```
用法:
```
const intervalID = window.setInterval((()=>{console.log('Hello world')}),1000);
```

Q:如果要清除setInterval呢?
利用clearInterval

用法:
```
const intervalID = window.setInterval((()=>{console.log('Hello world')}),1000);
window.clearInterval(intervalID);
```

### 小結
「雖然 JavaScript 有著非同步事件的特色，但仍是依循單一執行緒的規則運作。 換句話說，當我們在主要執行緒內工作的時間太久，就勢必會延遲 Queue Callback 的執行。」－－引用參考資料內文

所以，要計算非常精準的時間，setTimeout、setInterval，都一定會有誤差，可以用server端的正確時間去修正誤差，讓他至少在一個合理的範圍內。

此外，仍有IIFE(Immediately Invoked Function Expression)、scope問題，待補。

參考資料:
https://kuro.tw/posts/2019/02/23/%E8%AB%87%E8%AB%87-JavaScript-%E7%9A%84-setTimeout-%E8%88%87-setInterval/
https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval
---
title: Arduino限流LED實驗
date: 2021-10-28 17:04:44
categories:
  - [Computer Science,電子電路]
---

### 前言
想以僅有的材料做個LED限流的實驗，並使用電路公式算出理論數值實作。

### 材料
1. LED 3MM 紅色 x 1 :發光二極體
2. Arduino版  x 1:任意一塊都可
3. 220歐姆電阻  x 3:紅紅棕金

### 理論部分
首先我們Arduino電源接3.3V，而LED紅的電流需要0.02A(20mA)電壓則是2.0-2.2V，姑且算2.0電壓。

這時3.3V怎樣降到2.0V?就是使用電阻
那要多少歐姆的電阻值呢?
透過歐姆定律
在電路學裏，歐姆定律（英語：Ohm's law）表明，導電體兩端的電壓與通過導電體的電流成正比，以方程式表示。
```
V=IR
```
(3.3-2.0) = 0.02 * R
1.3 = 0.02R
R = 1.3/0.02 
R = 65
所以我們需要65Ω(Ohm)的電阻
但天殺的= =肯定不會這麼剛好有65歐姆的電阻，這時就要用現有的電阻兜出來。
並聯電阻可以降低歐姆。
依照克希荷夫電路定律推倒後得到以下公式:
```
Rt = 1/(1/R1+1/R2+1/R3+...+1/Rn)
```
以現有的220歐姆電阻要降到65就要稍微計算一下

3顆:
1/(1/220+1/220+1/220) = 73
4顆:
1/(1/220+1/220+1/220+1/220) = 55

發現3顆電阻約可以到73很接近65了，第4顆則是會低過65。
我們使用電阻是為了降低電壓、限流，讓IC能夠正常啟用下避免過熱燒毀、穩定運作，所以差不多就使用3顆電阻並聯。

電路圖
{% asset_img 電路圖_fx.png %}

接線圖
{% asset_img 實際接線圖_fx.png %}

現實:
{% asset_img real_fx.png %}

### 總結

用理論公式推後就可以用任意的電壓、電阻來兜
避免IC燒壞、延長使用時間

((ps.懂了後會覺得一些範例書上只給一個電阻然後不告訴你為什麼、怎麼算實在太黑了。

**參考資料**
電阻色碼計算
https://zh.wikipedia.org/wiki/%E9%9B%BB%E9%98%BB%E8%89%B2%E7%A2%BC
電阻放置
https://zhidao.baidu.com/question/1610875004126323107.html
限流電阻計算器
https://gc.digitw.com/Program/Resistor4LED/Resistor4LED.htm
為什麼LED要加上電阻
http://yehnan.blogspot.com/2012/03/arduinoled220-ohm.html
控制LED
https://blog.jmaker.com.tw/arduino-tutorials-3/
歐姆定律
https://zh.wikipedia.org/wiki/%E6%AC%A7%E5%A7%86%E5%AE%9A%E5%BE%8B
並聯電路
https://zh.wikipedia.org/wiki/%E4%B8%A6%E8%81%AF%E9%9B%BB%E8%B7%AF#%E9%9B%BB%E9%98%BB%E5%99%A8
克希荷夫電路定律
https://zh.wikipedia.org/wiki/%E5%9F%BA%E7%88%BE%E9%9C%8D%E5%A4%AB%E9%9B%BB%E8%B7%AF%E5%AE%9A%E5%BE%8B#%E5%9F%BA%E7%88%BE%E9%9C%8D%E5%A4%AB%E9%9B%BB%E6%B5%81%E5%AE%9A%E5%BE%8B%EF%BC%88KCL%EF%BC%89
歐姆定律
https://physcourse.thu.edu.tw/mengwen/%E6%99%AE%E7%89%A9%E5%AF%A6%E9%A9%97/%E5%AF%A6%E9%A9%97%E9%A0%85%E7%9B%AE/%E6%AD%90%E5%A7%86%E5%AE%9A%E5%BE%8B/
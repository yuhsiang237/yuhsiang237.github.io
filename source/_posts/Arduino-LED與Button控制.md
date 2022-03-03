---
title: Arduino LED與Button控制
date: 2021-10-29 17:04:44
categories:
  - [Computer Science,電子電路]
---
### 前言
想以僅有的材料做個LED與按鈕控制實驗。
並且藉由撰寫程式來監控按鈕，並控制LED明暗。

### 材料
1. LED 3MM 紅色 x 1 :發光二極體 (1.8V～2.2V，5mA～20mA)
2. Arduino版  x 1:任意一塊都可 (UNO)
3. 220歐姆電阻(5%)  x 1:紅紅棕金
3. 2.2k歐姆電阻(5%)  x 5:紅紅紅金

首先是LED的線路，要讓LED能夠穩定且不燒壞的亮燈，要先計算電阻多少。
OUTPUT會給予5V，因此用歐姆定律
```
V = IR
```
(5-2)=0.02R
R=150ohm

但是LED其實可以低於20mA，規格是5mA～20mA
因此嘗試串聯一顆220歐姆電阻算看看:
(5-2)=I*220 
I=3/220
=0.013A
=13mA
還在範圍內!所以就用220歐姆電阻了!

再來是按鈕部分，這時要扯到一個新概念就是上拉電阻
上拉電阻:
在數位電路中，上拉電阻（英語：Pull-up resistors）是當某輸入埠未連接設備或處於高阻抗的情況下，一種用於保證輸入訊號為預期邏輯電平的電阻元件。他們通常在不同的邏輯裝置之間工作，提供一定的電壓訊號。

主要用上拉電阻協助我們按鈕提供穩定的訊號。
{% asset_img Pullup_Resistor.png %}
當開關斷開的時候，邏輯閘的輸入訊號被上拉到Vin；當開關閉合的時候，邏輯閘的輸入端與地面相連，輸入訊號接近0伏特。

通常上拉電阻都會接10K電阻，但我手邊只有2.2k所以串個5顆。

V=IR
5 = I*(2200*5)
I=0.0004A=0.4mA


{% asset_img LEDWithButton_架構圖.png %}

{% asset_img LEDWithButton_bb.png %}


**Code.ino**
```
const int BUTTON_PIN = 7;  // 按鍵的接腳
const int LED_PIN =  8;   // LED燈的接腳

int buttonState = 0;   // 按鈕的狀態

void setup() {
  Serial.begin(115200);
  pinMode(LED_PIN, OUTPUT);   //設定LED的PIN腳為輸出
  pinMode(BUTTON_PIN, INPUT); //設定按鈕的接腳為輸入，因為我們要讀取它的狀態
}

void loop() {
  buttonState = digitalRead(BUTTON_PIN);  //讀取按鍵的狀態
 
  if(buttonState == LOW){          //如果按鍵按了
    Serial.print(buttonState);
    digitalWrite(LED_PIN, HIGH);   //LED就亮了 
  }else{                           //如果按鍵是未按下
    Serial.print(buttonState);
    digitalWrite(LED_PIN, LOW);    //LED就不亮
  }
}
```

{% asset_img "Screen Shot 2021-10-19 at 11.02.02 PM.png" %}

{% asset_img "demo.gif" %}


### 總結
發現電路需要理論部分的加強，否則接出來的線路會有問題。
以此來說就是上拉電阻，如果不知道他的原理，會覺得接個5V出來是要做什麼。

**參考資料**
https://zh.wikipedia.org/wiki/%E4%B8%8A%E6%8B%89%E7%94%B5%E9%98%BB

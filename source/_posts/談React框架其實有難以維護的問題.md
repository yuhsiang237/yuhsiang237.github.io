---
title: 談React框架難以維護的問題
date: 2022-05-13 18:34:03
categories:
   - [Code,Javascript]
---
這篇聊一下過去在寫React實作上遇到的問題，以及本篇重點為什麼難以維護。
以下:
1. React如果選擇純Javascript，專案越大越難維護
2. 維護的前人是高手，接手的人是新手
3. 過度抽象的邏輯
4. JSX不好了解全局
5. 每個都元件，但不寫文件
6. 過度封裝的巢狀元件
7. 無固定寫法，套件選擇難以控制

#### 1.React如果選擇純Javascript，專案越大越難維護
因為javascript本身就有很多雷坑
1.await、async
2.javascript reference的問題
3.函式參數型態不明確，比如不知道傳進來的參數是陣列、物件、物件屬性的內容又是什麼。因為大多數React專案都是直接原生Javascript，造成容易犯原生的錯。
```javascript
const getOrderReceiptData({order}) => order?.detail?.receipt?.data
```
**解決方法**  
請使用Typescript、ESLint可以避免。


#### 2. 維護的前人是高手，接手的人是新手
因為React很自由，所以可能前人會ES6、React Hook、React Class、各種bind用法，並把它發揮的非常透徹。
這時接手的人必須每遇到一個就去查用法，直到補完所有用法才知道在寫什麼。

**解決方法**  
限制Code統一寫法。且至少要兩人，不然好懂難懂只有寫的人知道。

#### 3. 過度抽象的邏輯
```javascript
const getData({pid,od,o}) => { 
    //略 以下作一些複雜的pid、od、o的資料處理
}
```
添加一些自己覺得合理但其實超難讀的簡寫參數。
然後getData因為最後要取得data所以就抽象叫做getData，但不寫註解，讓人要盤完內容才能知道做什麼。一個還好，當A call B、B call C時，就會很可怕。

**解決方法**  
解決方法:Typescript、ESLint。

#### 4. JSX不好了解全局
因為需使用原生寫法造成三元運算子大量使用。
難以看懂原本畫面、動態變更後結果。
```javascript
<div>
{
    <div>
    A
    </div>?
    <span>
        <div>
        A
        </div>?
        <span>
        B
        </span>:
        <div>
        C
        </div>
    </span>:
    <div>
    C
    </div>
}
</div>
```
**解決方法**  
無解

#### 5. 每個都元件，但不寫文件
封裝的套件又沒說明文件時。
一個套件外面傳入N個參數，前往實作看到1000行js，很可怕。
要改一個小東西，要重寫也無法，因為全部吃那一套。
寫React真的別閉門造車，特別是不寫說明文件的人，請去用開源套件謝謝...。
```javascript
<CustomTable
    data={data}
    key={id}
    isDeleted={checkedList}
    ...
/>
<Dn
    isDeleted={checkedList}
/>
<RSpan
    data={data}
/>
```

**解決方法**  
寫說明文件、範例檔案

#### 6. 過度封裝的巢狀元件
當你一直前往發現套件裡面還有更多套件，像是俄羅斯娃娃，而這些俄羅斯娃娃又拆的很散。
**解決方法**  
無解，現行前端的通病。

#### 7. 無固定寫法，套件選擇難以控制
React官方沒有固定範例寫法，只提供基本library，就是基礎ES6 JS去寫，所以每個人寫出的code都不一樣，如果又是新手剛學所寫的，一開始架構沒規劃好，後面就是建立一個歪掉的建築。
在套件依賴上也很多選擇，造成版本依賴性問題。
所以React...維護的專案會很可怕。
相比Vue，Vue官方做的很好，完整的範例可以參考，大家寫法也99%一致。

**解決方法**  
無解。

### 結論
其實很多人鼓吹JQuery淘汰，都改去寫React是有問題的，因為React容易要改一個東西要讀前人的一串技術債，改法絕對沒JQuery輕鬆而且會改到很崩潰。
不是元件化了?那前提是元件化是合理的...當發現一些不合理參數傳進去、設計奇怪的元件、客製化又封裝...，如果你是接手的，JQuery還痛快些。

綜合上述，寫React會遇到上述的這些機率超高，所以難以維護，塊陶...。

那Vue呢?
Vue有官方完整應用範例，像人在看的HTML Template不像React的jsx，畫面好上手、包好的語法糖，後面接手的人都不會太有壓力。即使Vue是用js寫不是Typescript，因為他不用像React過度抽象化，很多官方語法糖都包好了。

然後如果是大專案，Typescript是必須的，否則你的js會在多人改來改去下炸鍋。

以上是個人體驗後的結論。
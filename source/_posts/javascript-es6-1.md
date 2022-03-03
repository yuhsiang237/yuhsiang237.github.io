---
title: javascript ES6 整理(上)
date: 2021-08-24 17:30:22
categories:
   - [Code,Javascript]
---


陣列、變數、函數。

## 1. let const 變數宣告

```
let  x = 10
{  
  let x = 2
  console.log(x) // 2 
}
console.log(x) // 10
```

let，有自己的區塊作用域，不會蓋掉底下宣告的變數

```
var x = 10
for(var i=0;i<5;i++){
   var x = 5
   console.log(x) //5
}
console.log(x) // 5
```
var，沒有自己的區塊作用域，會蓋掉底下宣告的變數


```
const x = 10; 
```
const用來宣告常數不會被改變，不過如果是物件，仍然可以使用提供的方法去改變裡面的數值。

**結論**
如果沒有必要需求還是用let或const好，因為不會往下覆之後宣告的變數。

## 2. arrow function
```
<button>按鈕</button>
<script>
// 使用 addEventListener 監聽事件
var button = document.querySelector('button');
var fn_arrow = () => {
  // 建立 function 時 this 指向 Window
    console.log(this.constructor.name);  // 執行 function 時 this 指向 Window
};
var fn = function(){
  // 建立 function 時 this 指向 Window
    console.log(this.constructor.name);  // 執行 function 時 this 指向 HTMLButtonElement
};

button.addEventListener('click', fn_arrow);
button.addEventListener('click', fn);
```
**結論**
ES6可以簡化ES5的function寫法(匿名函式)等。
ES6 arrow function的this都指向Window，是固定的。
ES5 this 指向在運作時的作用域中，以上範例就是在button的作用域。
看起來如果只是單純callback那ES6箭頭函式會簡潔些，但如果要操作某作用域還是得用ES5寫法。
參考:https://ithelp.ithome.com.tw/articles/10195669
(*未完還有this問題待補)

## 3. 陣列操作
**forEach**
```
const array = [
  1,2,3,4,5
];

array.forEach((item,index) => console.log(index,item));
```
遍歷元素使用，基本上可以取代掉for。但在裡面async/await不能生效，要用for。
此外，不會回傳任何值。
參考:
https://stackoverflow.com/questions/37576685/using-async-await-with-a-foreach-loop

**map**
```
const array1 = [1, 2, 3 ,4];
const array2 = array1.map(item => item*2);
console.log(array1) // [ 1, 2, 3, 4 ]
console.log(array2) //  [ 2, 4, 6, 8 ]
```
會透過函式內所回傳的值組合成一個新的陣列
並不會改變原陣列

**reduce**

```
const array1 = [1, 2, 3, 4];
const reducer = (accumulator, currentValue) => accumulator + currentValue;

// 1 + 2 + 3 + 4
console.log(array1.reduce(reducer));
// output: 10

// 5 + 1 + 2 + 3 + 4
console.log(array1.reduce(reducer, 5));
// output: 15
```
累加器，通常是要把所有陣列內容加起來才會用到，將陣列化為單一值。

參考:
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce

**from**
```
const myArr = Array.from("ABCDEFG");
console.log(myArr)  // 拆數組[ "A", "B", "C", "D", "E", "F", "G" ]
```
幾乎很少用到，可將字串拆成字組。而在IE上無法運行此函式。

**find**
```
const numbers = [4, 9, 16, 25, 29];
const target = numbers.find(item=>item === 4);
console.log(target)
```
通常在找陣列中的某元素(物件)會用到，返回是單一的元素。


**keys**
```
const fruits = ["Banana", "Orange", "Apple", "Mango"];
const keys = fruits.keys();

let text = "";
for (let x of keys) {
  console.log(x)
}

//0 1 2 3
```
取得陣列所有index

**findIndex**
```
 const numbers = [4, 9, 16, 25, 29];
 const targetIndex = numbers.findIndex((value, index)=>value > 9);
 console.log(targetIndex) // 2 (即數字16陣列位置)
```
返回第一個符合條件的陣列index。




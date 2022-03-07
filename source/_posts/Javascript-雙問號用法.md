---
title: Javascript 雙問號、可選鏈用法
date: 2022-03-07 10:57:25
categories:
   - [Code,Javascript]
---
在寫Javascript時會看到??的語法或?.的用法。  
在這稍微做解釋:  

**?.可選鏈:**
作用:可以簡化寫法，因為Javascript如果強制叫出不存在的物件會出現錯誤，所以原本寫法都要寫很長去檢查物件屬性是否存在。
```javascript
const user = {data:{address:"my address"}}
// 原本寫法 output:my address
const userAddress = user.data && user.data.address
// 替換如下 output:my address
const userAddress = user.data?.address
```
可以減少巢狀物件取的屬性的程式碼。

**??:**
作用:左方為null或undefined則會直接回傳右方的值。
```javascript
const detail = {data:{address:"my address"}}
// 原本寫法 output:10
const detailAmount = (detail.data.amount && detail.data.amount) ? detail.data.amount : 10 ;
// 替換如下 output:10
const detailAmount = detail.data?.amount ?? 10;
```
上面因為找不到amount所以接回傳10，有的話則回傳amount


### 總結
感覺未來的Javascript會充斥著 「??」、「?.」
到時候又配上Javascript函式寫法感覺閱讀性會複雜了。(雖然簡化語法，但學習門檻變高)
看自己手上的專案斟酌吧。

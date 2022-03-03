---
title: C# Properity(屬性) 與 Field(欄位)
date: 2022-01-13 13:33:52
categories:
  - [Code,C#]
---
在這篇主要介紹Properity(屬性)、Field(欄位)在物件導向class中的差別。

### Properity(屬性)
1.以public為主，能夠讓外部能存取。
2.包含get、set訪問器(能要求欄位只能讀或只能寫)。
3.可以增加邏輯處理，在改變一個欄位的值的時候希望改變物件的其它一些狀態，舉例:更改某外部公開屬性值x為1時，另一個內部私有欄位y需要連動更改數值為2。
4.不佔有實際內存，主要為get;set;存取私有欄位資料。

### Field(欄位)
1.以private為主(有時會以protected)，主要以class內部使用為主。
2.命名會加_在前面。
3.可能為靜態值所以設定為readonly
4.佔有實際內存、位址。

### 範例
```
public class User
{
    private string _name;// _name為欄位
    public string Name   // Name為屬性，它含有程式碼塊
    {
        get
        {
            // 可以在此添加額外會影響的私有變數
            return _name;// 讀取（返回_name值）
        }
        set 
        {
            _name = value;// 為_name賦值
            // 可以在此添加額外會影響的私有變數
        }
    }
}
```
### 總結
為什麼要使用屬性為什麼不用欄位就好?
因為能夠確保內部私有資料的安全性，並且能夠透過get;set;方式訪問時能夠增加邏輯處理、方便限制可讀、可寫。

### 參考資料
https://kevintsengtw.blogspot.com/2011/09/property-field.html?m=1#:~:text=Field%EF%BC%9A_FullDescription%E7%82%BA%E7%89%A9%E4%BB%B6%E7%A7%81%E6%9C%89,%E4%BE%86%E5%AD%98%E6%94%BE%E5%B1%AC%E6%80%A7%E7%9A%84%E8%B3%87%E6%96%99%E3%80%82&text=%E7%AF%80%E9%8C%84%E6%95%B4%E7%90%86%EF%BC%9A,%E9%9C%80%E8%A6%81%E5%AE%8C%E5%85%A8%E7%9B%B8%E5%90%8C%E7%9A%84%E8%AA%9E%E6%B3%95%E3%80%82
https://www.itread01.com/content/1548110544.html
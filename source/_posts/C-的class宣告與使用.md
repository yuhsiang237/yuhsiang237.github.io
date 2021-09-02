---
title: C# class宣告及使用
date: 2021-09-02 23:30:39
categories:
  - [程式語言,C#]
---

在C++後有很多物件導向(Object-oriented programming，OOP)的語言竄起，如JAVA、VB、C#等等。
未來可能會以C#為主要語言，所以就來練習一下C#的類別宣告吧。

### 物件導向程式設計
是種具有物件概念的程式設計典範，同時也是一種程式開發的抽象方針。它可能包含資料、特性、程式碼與方法。物件則指的是類別（class）的實例。物件導向程式設計可以看作一種在程式中包含各種獨立而又互相呼叫的物件的思想，這與傳統的思想剛好相反：傳統的程式設計主張將程式看作一系列函式的集合，或者直接就是一系列對電腦下達的指令。

物件導向程式語言包含Common Lisp、Python、C++、Objective-C、Smalltalk、Delphi、Java、Swift、C#、Perl、Ruby、JavaScript(雖然javascript通常都用function在寫，但ES6有class) 與 PHP等。 

### C# class宣告及使用 
透過人(Person)的類別class，宣告一個人的實例。

```
using System;
					
public class Program
{
	public class Person{
		
		private string name; // 內部私有的name變數
		
		public string Name {
			get{return name;} // getter 取出私有的name變數
			set{this.name = value;} // setter 將value設定到私有的name變數
		}
		
		public Person(){ // 建構子
			this.name = "沒有名子";
		}
		
		public Person(string name){ // 多載(Overload)的建構子，可初始化name
			this.name = name;
		}
	
	}
	
	public static void Main()
	{
		Person person= new Person();// 宣告一個人的實例
		Console.WriteLine(person.Name);// 透過getter取出變數
		person.Name = "Yu Hsiang"; // 透過setter設定變數
		Console.WriteLine(person.Name); // 透過getter取出變數
	}
}
```
輸出
```
沒有名子
Yu Hsiang
```

### 結語
為什麼要用getter、setter?
為了避免誤改到變數及維護性的需求。
比如，未來想要加一個*在每個人名前面，這時如果沒有setter、getter，就得改每一行的程式碼。
如果有setter、getter就像有一個緩衝的函式，可以讓我們直接在這裏面修正好值再存入。
當然，也有人不寫getter或setter，也是能運作，見仁見智，符合本身團隊需求就可以了。

C#有點簡化了getter、setter，以前在寫C++時都會寫成setName、getName，再回傳本身指標，就能無限點下去。
https://github.com/yuhsiang237/C-plusplus-homework/blob/master/HomeworkBoss/final/2/2/Place.cpp

這練習想起了大學的C++作業:
https://github.com/yuhsiang237/C-plusplus-homework

**參考資料**
https://zh.wikipedia.org/wiki/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1
https://docs.microsoft.com/zh-tw/dotnet/csharp/fundamentals/types/classes
https://www.w3schools.com/cs/cs_properties.php
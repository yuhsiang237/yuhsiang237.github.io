---
title: C# Queue與Stack操作
date: 2021-08-30 20:11:10
categories:
   - [Code,C#]
---
### 前言
為了熟悉C#處理資料的方式，所以找了兩種比較常見的資料結構來練習。
分別是Queue(佇列)、Stack(堆疊)。

### Queue
先進先出

```
using System;
using System.Collections;

public class Program
{
	public static void Main()
	{
		Queue q = new Queue();
		q.Enqueue("hello"); // 向 Queue 的末尾新增一個物件。
		q.Enqueue(32);
		q.Enqueue(5);
		q.Enqueue('N');
		
		Console.WriteLine("印出Queue的內容:");
		foreach(var item in q){
			Console.WriteLine(item);
		}
		
		Console.WriteLine("取出一個資料:");
		var result = q.Dequeue(); // 從 Queue 的前端取出一個資料
		Console.WriteLine(result);
        
		Console.WriteLine("轉成陣列印出:");
		var array = q.ToArray();
		foreach(var item in array){
			Console.WriteLine(item);
		}
	}
}
```

輸出:
```
印出Queue的內容:
hello
32
5
N
取出一個資料:
hello
轉成陣列印出:
32
5
N
```

### Stack
先進後出
```
using System;
using System.Collections;

public class Program
{
	public static void Main()
	{
		Stack s = new Stack();
		s.Push("hello"); // 向 Stack 的頂部新增一個物件。
		s.Push(32);
		s.Push(5);
		s.Push('N');
		
		Console.WriteLine("印出Stack的內容:");
		foreach(var item in s){
			Console.WriteLine(item);
		}
		
		Console.WriteLine("取出一個資料:");
		var result = s.Pop(); // 移除並返回在 Stack 的頂部的物件。
		Console.WriteLine(result);
		
		Console.WriteLine("Peek觀察頂部物件但不移除:");
		result = s.Peek(); // Peek觀察頂部物件但不移除
		Console.WriteLine(result);
		
		Console.WriteLine("轉成陣列印出:");
		var array = s.ToArray();
		foreach(var item in array){
			Console.WriteLine(item);
		}
	}
}
```
輸出:
```
印出Stack的內容:
N
5
32
hello
取出一個資料:
N
Peek觀察頂部物件但不移除:
5
轉成陣列印出:
5
32
hello
```

### 結語
C#除了已經內置了基礎的佇列(Queue)、堆疊(Stack)，還內建了一些簡易的語法。
如Contains，可以對整個資料結構去檢查是否存在某個特定元素。亦有toArray()可以快速的把資料結構轉為陣列型態。

**參考資料**
https://www.itread01.com/content/1550138429.html?fbclid=IwAR3ty4HR0DlmSWRMl19IFBgESeXM2aH5GsJxxcKHMGdzY6WIJm90brUzsvI
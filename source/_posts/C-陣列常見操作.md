---
title: C#陣列常見操作
date: 2021-08-29 14:00:02
categories:
  - [Code,C#]
---
因為每種程式語言都有它特殊的宣告方式，因此就花時間整理一下，往後需要使用時就能快速的熟悉回來。

### 陣列宣告
1.一維陣列
```
int [] array = new int[5];
Console.WriteLine(array[0]); // 輸出:0  陣列的元素會初始化為整數的元素類型預設值 0 。
```
2.二維陣列
```
int [,] array = new int[4,2];
Console.WriteLine(array[1,1]); // // 輸出:0  陣列的元素會初始化為整數的元素類型預設值 0 。
```
### 陣列初始化
1.一維陣列
```
int [] array = new int[]{1,2,3,4,5};
Console.WriteLine(array[0]); // 輸出:1 
```
2.二維陣列
```
int [,] array = new int[4,2]{{1,2},{3,4},{7,5},{4,8}}; // 在宣告時如果有初始值後面就必須要填滿那些範圍的元素
Console.WriteLine(array[2,1]); // 輸出5 
```
3.不規則陣列
感覺很少用到
```
int[][] array = new int[3][]; // 宣告時必須int[][] 而不是 int[,]
array[0] = new int[]{1,3};
array[1] = new int[]{5,3,3};
array[2] = new int[]{1,31,5};
Console.Write(array[2][1]);//輸出 31
```
4.隱含型別陣列
用var開頭，裡面的元素符合同一個型態即可
```
var array = new [] {"hi!",null,"world"};
Console.Write(array[2]);//輸出 world
```

### 遍歷陣列方式

1.使用for，如果處理多維較為方便，且比較直覺
```
int [] array = new int[]{1,2,3,4,5};
for(int i=0;i<array.Length;i++){
	Console.Write(array[i]); 
}  // 輸出:12345
```
2.foreach，如果是單維想簡潔些可以用
```
int [] array = new int[]{1,2,3,4,5};
foreach(int num in array){
    Console.Write(num);
} // 輸出:12345
```

### 把陣列傳入函式
```
using System;			
public class Program
{
	public static void printArray(int [] arr){
		for(int i=0;i<arr.Length;i++){
			Console.Write(arr[i]);
		}
	}
	public static void Main()
	{
		int [] array = new int[]{1,2,3,4,5};
		printArray(array); // 輸出12345
	}
}
```

### 結語
C#強型別的語言在宣告陣列確實比較麻煩。但相對的，這種寫法比較好"除錯"。
如果平時寫弱型態的如javascript，那要多記一下寫法。
而既然使用了"new 運算子"就是傳址了，要稍微小心寫法。

**參考資料**
https://docs.microsoft.com/zh-tw/dotnet/csharp/programming-guide/arrays/
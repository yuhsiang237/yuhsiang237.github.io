---
title: C#亂數
date: 2021-08-30 20:53:06
categories:
  - [程式語言,C#]
---
### 前言
亂數(Random)是在程式語言中常見的內建類別。
如陣列洗牌、模擬骰子等等都會常用到，所以就來寫一下他的用法。

### Random
1.Next用法 
32 位元帶正負號的整數大於或等於 0，並且小於 MaxValue。

```
using System;

public class Program
{
	public static void Main()
	{
		Random r = new Random(); //宣告亂數種子
		int result = r.Next(1,7); // 可能回傳1~6的整數數字，因為Next第二個參數是小於，以此例為小於7
		Console.WriteLine(result); // 可能回傳1~6的整數數字
	}
}
```
輸出結果
```
2
```

2.Random洗亂陣列
以array長度洗牌，隨意骰出符合陣列的兩個index，比較是否相同，不相同就將其交換。
最後得到一個洗牌後陣列。
```
using System;

public class Program
{
	public static void Main()
	{
		Random r = new Random(); //宣告亂數種子	
		
		int [] array = new int[5]{1,2,3,4,5}; // 宣告陣列並初始化
		
		for(int i=0;i<array.Length;i++){
			int index1 = r.Next(0,array.Length); 
			int index2 = r.Next(0,array.Length); 
			if(index1 != index2){ // 交換陣列內容
				int temp = array[index1];
				array[index1] = array[index2];
				array[index2] = temp;
			}
		}
		Console.WriteLine("陣列洗牌後結果:");
		foreach(int item in array){
			Console.WriteLine(item);
		}
	}
}
```
輸出結果
```
陣列洗牌後結果:
2
4
1
3
5
```
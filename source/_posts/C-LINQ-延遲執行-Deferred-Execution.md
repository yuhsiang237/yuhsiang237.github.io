---
title: C# LINQ 延遲執行 (Deferred Execution)
date: 2022-01-13 14:14:52
categories:
  - [Code,C#]
---
### 前言
在這篇中主要筆記LINQ 延遲執行的特性。
一般我們如果使用純SQL語言去與資料庫查詢通常一下指令就會被馬上執行，而LINQ卻不一樣，是直到某個觸發點他才會開始運作。
即寫Select時並不會馬上運作。

### 常見的觸發點
1.foreach(var item in items)
2.轉為ToList(), ToArray(), ToDictionary()，因為需巡覽才能轉型，所以會直接執行。
3.取得彙總結果的聚合函数 Count(), Max(), Sum(), First(), Last()。

### 主要原因
延遲執行的基礎 － Iterator、yield
1. 您只要提供 Iterator，它會只往返於類別中的資料結構。當編譯器偵測到您的 Iterator 時，它會自動產生 IEnumerable 或 IEnumerable<T> 介面的 Current、MoveNext 和 Dispose 方法。
2. Iterator 程式碼會使用 yield return 陳述式輪流傳回各元素。yield break 則會結束反覆運算。

關鍵:使用 yield 關鍵字，C# 會自動將該 block 實作為 iterator 方式執行。
```
using System;
using System.Collections;

namespace DeferredExec
{
    class Program
    {
        public class Data : IEnumerable
        {
            public IEnumerator GetEnumerator()
            {
                Console.WriteLine("yield 1");
                yield return 1;
                Console.WriteLine("yield 2");
                yield return 2;
                Console.WriteLine("yield 3");
                yield return 3;
            }
        }
        static void Main(string[] args)
        {
            Data data = new Data();
            foreach(var item in data)
            {
                Console.WriteLine(item);
            }
        }
    }
}
```
實作IEnumerable介面，並且撰寫方法GetEnumerator，yield會把每個區塊視為iterator(迭代器)。
而foreach中取得每個item在逆向工程可以見到其實是呼叫GetEnumerator取得一個IEnumerator(即iterator)。

有這觀念再拉回來看LINQ的方法，可以發現是IEnumerable。
Where:
```
public static IEnumerable<TSource> Where<TSource>(this IEnumerable<TSource> source, Func<TSource, bool> predicate)
```
Select:
```
public static IEnumerable<TResult> Select<TSource, TResult>(this IEnumerable<TSource> source, Func<TSource, TResult> selector)
```
想知道是不是延遲執行可以看返回值是否為以下3種物件:
```
IEnumerable<TSource>
```
```
IEnumerable<IGrouping<TKey, TSource>>
```
```
IOrderedEnumerable<TSource>
```
### 範例
```
using System;
using System.Collections;
using System.Collections.Generic;
using System.Linq;

namespace DeferredExec
{
    class Program
    {
        private static void Main(string[] args)
        {
            var people = GetPeople();
            var names = people
                .Where(person => person.Age > 18)
                .Select(person => person.Name);

            foreach (var name in names)
            {
                Console.WriteLine(name);
            }
        }

        private static IEnumerable<Person> GetPeople()
        {
            yield return new Person { Id = 1, Name = "John", Age = 36 };
            yield return new Person { Id = 2, Name = "Bill", Age = 6 };
            yield return new Person { Id = 3, Name = "Steve", Age = 23 };
        }

        public class Person
        {

           private int _id;
           private string _name;
           private int _age;
           public int Id {
                get {
                    return _id;
                }
                set
                {
                     _id = value;
                }
            }

            public string Name
            {
                get
                {
                    return _name;
                }
                set
                {
                    _name = value;
                }
            }
            public int Age
            {
                get
                {
                    return _age;
                }
                set
                {
                    _age = value;
                }
            }
        }
    }
}

```
在這範例中使用泛型(Generic)的IEnumerable
```
IEnumerable<T>
```
因為Select、Where方法是IEnumerable，所以會在foreach觸發GetEnumerator時執行，進而達到延遲執行的效果。

### 延遲執行好處
1.可以再添加額外的表達式，如主要的查詢表達式後面可以添加子表達式過濾作為最終結果。
2.取得資料是最新的，因為當下才執行

### 總結
可以得知C#的LINQ為什麼能夠延遲執行是因為採用IEnumerable介面，裡面會有yield區塊形成迭代器，所以直到foreach執行時才會開始觸發。

### 參考資料
https://dotblogs.com.tw/hatelove/2013/09/10/csharp-linq-deferred-execution-introduction
https://dotblogs.com.tw/hatelove/2012/05/10/introducing-foreach-ienumerable-ienumerator-yield-iterator
https://dotblogs.com.tw/regionbbs/2012/01/12/linq_deferred_execution
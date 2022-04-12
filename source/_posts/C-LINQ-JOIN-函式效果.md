---
title: C# LINQ JOIN 函式效果
date: 2022-04-12 14:27:25
categories:  
  - [Code,C#]
---
### 前言
因為平時都是寫lambda寫法的LINQ，而在近期看到函式方式的寫法，所以紀錄一下作法。

### JOIN函式定義
```csharp
public static System.Collections.Generic.IEnumerable<TResult> 
Join<TOuter,TInner,TKey,TResult> (
    this System.Collections.Generic.IEnumerable<TOuter> outer,
    System.Collections.Generic.IEnumerable<TInner> inner, 
    Func<TOuter,TKey> outerKeySelector, 
    Func<TInner,TKey> innerKeySelector,
    Func<TOuter,TInner,TResult> resultSelector, 
    System.Collections.Generic.IEqualityComparer<TKey>? comparer);
```
參數部分:
```
outer
IEnumerable<TOuter>
要聯結的第一個序列。

inner
IEnumerable<TInner>
要加入第一個序列的序列。

outerKeySelector
Func<TOuter,TKey>
用來從第一個序列各個項目擷取聯結索引鍵的函式。

innerKeySelector
Func<TInner,TKey>
用來從第二個序列各個項目擷取聯結索引鍵的函式。

resultSelector
Func<TOuter,TInner,TResult>
用來從兩個相符項目建立結果項目的函式。

comparer
IEqualityComparer<TKey>
用來雜湊及比較索引鍵的 IEqualityComparer<T>。
```
### 實作
簡單來講就是拿主表資料去JOIN另一張關聯表，而x、y則是塞進去的key值。  
在這裡以User為主表，UserDetail為關聯表。  
原本4筆資料的User，經過與UserDetail JOIN關聯，於是剩下兩筆資料。  
```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using System.Linq;

namespace JoinSample
{

    public class User
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }

    public class UserDetail
    {
        public int UserId { get; set; }
        public string PhoneNumber { get; set; }
    }


    public class UserData
    {
        public int Id { get; set; }

        public string UserName { get; set; }
        public string PhoneNumber { get; set; }
    }

    internal class Program
    {
        static void Main(string[] args)
        {
            var users = new List<User>
            {
                new User
                {
                    Id =1,Name="大雄",
                },
                new User
                {
                    Id =2,Name="多拉A夢",
                },
                new User
                {
                    Id =3,Name="胖虎",
                },
                new User
                {
                    Id =4,Name="小夫",
                }
            };

            var userDetails = new List<UserDetail> {
                new UserDetail{ UserId=1,PhoneNumber="0911111111"},
                new UserDetail{ UserId=2,PhoneNumber="0922222222"}
            };

            var userData = users.Join(
                userDetails,
                x => x.Id,
                y => y.UserId,
                (x, y) => new UserData
                {
                    Id = x.Id,
                    PhoneNumber = y.PhoneNumber,
                    UserName = x.Name
                });

            foreach (var item in userData)
            {
                Console.WriteLine(item.Id);
                Console.WriteLine(item.UserName);
                Console.WriteLine(item.PhoneNumber);
            }
        }
    }
}

```

output:
```
1
大雄
0911111111
2
多拉A夢
0922222222
```

### 參考資料
- https://docs.microsoft.com/zh-tw/dotnet/api/system.linq.enumerable.join?view=net-6.0
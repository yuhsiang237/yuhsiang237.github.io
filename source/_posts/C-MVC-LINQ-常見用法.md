---
title:  '[Day15] C# MVC LINQ常見用法 - C#&AspNetCore'
date: 2021-10-11 10:17:57
categories:
  - [程式語言,C#]
---

在上回 {% post_link 'C-MVC-LINQ基礎用法' '[Day14] C# MVC LINQ基礎用法 - C#&AspNetCore'%}  ，我們介紹了LINQ基本用法。

而這回就要介紹LINQ常用的幾個JOIN、LEFT JOIN作法。


### 實作
先附上測試資料:
```
//　產品
Product[] products = new Product[] {
  new Product { Id = 1 ,Name = "PS4",Price =11000   } ,
  new Product { Id = 2 ,Name = "PS5",Price =15000   },
  new Product { Id = 3 ,Name = "Switch",Price=9900 },
  new Product{ Id= 4 ,Name="XBOX Series X",Price=15000   }
};

//　分類
ProductType[] productTypes = new ProductType[] {
  new ProductType {Id = 1 ,Name = "Game"} ,
  new ProductType { Id = 2 ,Name = "nintendo" } ,
  new ProductType { Id =3 ,Name = "Sony" }
};


//　產品與分類關系表
Product_ProductTypeRelationship[] product_ProductTypeRelationships = new Product_ProductTypeRelationship[] {
  new Product_ProductTypeRelationship {
      ProductID = 1 ,ProductTypeID = 1
  } ,
  new Product_ProductTypeRelationship {
      ProductID = 1 ,ProductTypeID = 3
  } ,
  new Product_ProductTypeRelationship {
      ProductID = 2 ,ProductTypeID = 1
  } ,
  new Product_ProductTypeRelationship {
      ProductID = 2 ,ProductTypeID = 3
  },
  new Product_ProductTypeRelationship {
      ProductID = 3 ,ProductTypeID = 1
  } ,
  new Product_ProductTypeRelationship {
      ProductID = 3 ,ProductTypeID = 2
  }
};
```
#### 1.LINQ實作INNER JOIN
```
public void linqInnerJoinExample()
{
    var query =
        from product in products
        join product_ProductTypeRelationship in product_ProductTypeRelationships on product.Id equals product_ProductTypeRelationship.ProductID
        join productType in productTypes on product_ProductTypeRelationship.ProductTypeID equals productType.Id
        select new
        {
            product.Id,
            product.Name,
            product.Price,
            productTypeName = productType.Name
        };
    var result = query.ToList();
}
```
結果:
{% asset_img "1.png" %}
說明:因為是InnerJoin，都要有重疊才會關聯出，而XBOX Series X的Id沒有在product_ProductTypeRelationships裡面，所以最後沒有被顯示出來。
基本上跟原生SQL的寫法挺像的。

#### 2.LINQ實作LEFT JOIN 

寫法1:拆兩個Query
```
public void linqLeftJoinExample()
{
    // product 跟 product_ProductTypeRelationship 先left join 
    var query1 =
        from product in products
        join product_ProductTypeRelationship in product_ProductTypeRelationships on product.Id equals product_ProductTypeRelationship.ProductID
        into tmp
        from product_ProductTypeRelationship in tmp.DefaultIfEmpty()
        select new
        {
            product.Id,
            product.Name,
            product.Price,
            ProductTypeID = product_ProductTypeRelationship?.ProductTypeID
        };

    // query1結果再跟 productTypes left join 
    var query2 =
        from q in query1
        join productType in productTypes on q.ProductTypeID equals productType.Id
        into tmp
        from product_ProductTypeRelationship in tmp.DefaultIfEmpty()
        select new
        {
            q.Id,
            q.Name,
            q.Price,
            ProductName = product_ProductTypeRelationship?.Name
        };
    var result = query2.ToList();
}
```
結果:
{% asset_img "2.png" %}
說明:可以得到XBOX Series X了。
因為LINQ沒有LEFT JOIN這種簡單作法，只能靠DefaultIfEmpty來操作，作法比較複雜、難直覺判斷。
((ps.不是要用來讓大家好用的查詢嗎?搞複雜了

作法2:組多個DefaultIfEmpty
```
public void linqLeftJoinExample2()
{
    var query =
        from a in products
        join b in product_ProductTypeRelationships on a.Id equals b.ProductID
        into result1
        from ab in result1.DefaultIfEmpty()
        join c in productTypes on ab?.ProductTypeID equals c.Id
        into result2
        from abc in result2.DefaultIfEmpty()
        select new{
            a.Id,
            a.Name,
            a.Price,
            ProductName = abc?.Name
        } ;
    var result = query.ToList();
}
```
結果:
{% asset_img "3.png" %}
說明:可以得到XBOX Series X了。
這種多個DefaultIfEmpty組法感覺蠻難讀的。
要獨立出一個值，導致得用a、b、c來組不然難知道誰是誰，就很難閱讀
且要小心有些值讀出來是空的要使用「?.」的做法，把empty的值設定為null才有辦法繼續下去，否則會執行到中間就報錯了。

以此，就是ab?.ProductTypeID，因為ab做完DefaultIfEmpty時可能ProductTypeID是為empty的，要用這ProductTypeID再去做下個join可能會出錯。

#### 3.組成階層方式
```
public void linqLeftJoinExample3()
{

    var p = (from a in products
                select new ProductViewModel
                {
                    Id = a.Id,
                    Name = a.Name,
                    Price = a.Price,
                }).ToList();

    foreach (var item in p)
    {
        var productTypeIDs = from a in product_ProductTypeRelationships
                                where a.ProductID == item.Id
                                select a.ProductTypeID;

        var productTypeData = from a in productTypeIDs
                                join b in productTypes on a equals b.Id
                                select new ProductType
                                {
                                    Id = b.Id,
                                    Name = b.Name
                                };
        item.ProductTypes = new List<ProductType>();
        foreach (var x in productTypeData)
        {
            item.ProductTypes.Add(x);
        }
    }
    var result = p;
    string jsonData = JsonConvert.SerializeObject(result);
    System.Diagnostics.Debug.WriteLine(jsonData);
}
```

結果:
```
[
  {
    "Id": 1,
    "Name": "PS4",
    "Price": 11000,
    "ProductTypes": [
      {
        "Id": 1,
        "Name": "Game"
      },
      {
        "Id": 3,
        "Name": "Sony"
      }
    ]
  },
  {
    "Id": 2,
    "Name": "PS5",
    "Price": 15000,
    "ProductTypes": [
      {
        "Id": 1,
        "Name": "Game"
      },
      {
        "Id": 3,
        "Name": "Sony"
      }
    ]
  },
  {
    "Id": 3,
    "Name": "Switch",
    "Price": 9900,
    "ProductTypes": [
      {
        "Id": 1,
        "Name": "Game"
      },
      {
        "Id": 2,
        "Name": "nintendo"
      }
    ]
  },
  {
    "Id": 4,
    "Name": "XBOX Series X",
    "Price": 15000,
    "ProductTypes": []
  }
]
```
說明:用foreach搭配LINQ用組的方式去做出階層資料，並且新增ProductViewModel做最後的資料規格呈現。
這種複雜度O(n^2)不是很好，未來想到好的方式再更改。
且要注意第10行要先ToList()，執行LINQ，否則下面的foreach會造成每次初始化。
如果以前端要資料來講，這樣是最好格式，可以不用手動組。
此外，這是多對多的資料關聯，是最複雜的情況。

### 總結
介紹了資料常見的處理JOIN、LEFT JOIN。
覺得要處理複雜查詢用LINQ真的不太適合...，因為太難閱讀XD?。
LINQ有他強型別、Intellisense的好處，但想到有些情況要LEFT JOIN 5張時應該會蠻崩潰的。
可能就得組簡單的LINQ再搭配迴圈分步驟做，效能會差一點。

總之，在複雜的查詢我會用「原生作法搭配Dapper」或是「簡單的LINQ搭程式用迴圈做」。

更多LINQ用法可以查看:
https://docs.microsoft.com/zh-tw/dotnet/csharp/linq/

**參考資料**
https://dotblogs.com.tw/yc421206/2014/07/11/145907
https://ad57475747.medium.com/linq%E5%AD%B8%E7%BF%92%E7%AD%86%E8%A8%98-6-join-%E5%A4%9A%E8%A1%A8%E5%96%AE%E5%A4%9A%E6%A2%9D%E4%BB%B6%E5%BC%8F-4076c487264f
http://vito-note.blogspot.com/2013/04/linq-3-join.html
https://docs.microsoft.com/zh-tw/dotnet/csharp/linq/perform-left-outer-joins
https://blog.csdn.net/yuanxiang01/article/details/111176654
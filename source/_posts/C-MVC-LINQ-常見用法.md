---
title:  '[Day15] C# MVC LINQ常見用法 - C#&MVC入門'
date: 2021-10-11 10:17:57
categories:
  - [程式語言,C#]
---

在上回 {% post_link 'C-MVC-LINQ基礎用法' '[Day14] C# MVC LINQ基礎用法 - C#&MVC入門'%}  ，我們介紹了LINQ基本用法。

而這回就要介紹LINQ常用的幾個作法。


### JOIN
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

2.LINQ實作LEFT JOIN


(未完。

**參考資料**
https://dotblogs.com.tw/yc421206/2014/07/11/145907
https://ad57475747.medium.com/linq%E5%AD%B8%E7%BF%92%E7%AD%86%E8%A8%98-6-join-%E5%A4%9A%E8%A1%A8%E5%96%AE%E5%A4%9A%E6%A2%9D%E4%BB%B6%E5%BC%8F-4076c487264f
http://vito-note.blogspot.com/2013/04/linq-3-join.html
https://docs.microsoft.com/zh-tw/dotnet/api/system.linq.queryable.selectmany?redirectedfrom=MSDN&view=net-5.0#System_Linq_Queryable_SelectMany__2_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Collections_Generic_IEnumerable___1____
---
title: 在C#中何時使用問號?去拿資料
date: 2022-12-07 14:40:20
categories:  
  - [Code,C#]
---
在處理C#語言中的物件，常會去拿物件底下子物件的值，常常會遇到錯誤即空物件錯誤，如底下程式碼的amount1變數。因為我們在拿amount1這個值時前面的OrderDetail物件還沒被初始化。  
這時就要使用問號"?"去取值避免拋錯，如amount2變數。  
資料在處理時總會拿到髒資料，所以先預判，避免出錯。  

程式碼如下:
```csharp
namespace Application
{
    /// <summary>
    /// DataProvider
    /// </summary>
    public class DataProvider
    {
        /// <summary>
        /// OrderDetail
        /// </summary>
        public OrderDetail OrderDetail { get; set; }
    }
    public class OrderDetail
    {
        /// <summary>
        /// amount
        /// </summary>
        public decimal? Amount;
    }

    internal class Program
    {
        static void Main(string[] args)
        {
            var data = new DataProvider();

            // amount1: error
            // It will be error. Due to the object, OrderDetail,  is null and doesn't initialize.
            // var amount1 = data.OrderDetail.Amount ?? default;

            // amount2: 0 
            // we can add the symbol "?" after the null object, and it can avoid being an error.
            var amount2 = data.OrderDetail?.Amount ?? default;
        }
    }
}

```

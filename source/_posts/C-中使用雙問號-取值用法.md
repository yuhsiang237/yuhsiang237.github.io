---
title: C#中使用雙問號??取值用法
date: 2022-12-07 14:55:29
categories:  
  - [Code,C#]
---
在C#中可以運用??來判斷前者是否為null，若為null則將後面值遞補上去。  
如下，因Amount為空，所以會套用後面的default。  
而default就是decimal的預設值0。  
因此，在最後會得到amount結果為0。
```csharp
namespace Application
{
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
            var orderDetail = new OrderDetail();
            var amount = orderDetail.Amount ?? default;
        }
    }
}
```
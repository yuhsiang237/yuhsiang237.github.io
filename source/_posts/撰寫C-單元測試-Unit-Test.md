---
title: 撰寫C#單元測試(Unit Test)
date: 2022-02-10 07:42:43
categories:
  - [程式語言,C#]
---
### 前言
在這回中主要介紹C#的單元測試。
將會以AspNetCore中的xUnit、Moq套件進行範例測試。

**至於為什麼要單元測試?**
因為能夠快速的驗證結果、在撰寫時也只要執行片段程式碼。  
對於有些程式碼容易有改A壞B的問題，能夠透過測試直接幫我們一次測完。  
讓我們只需要針對錯誤案例進行程式碼錯誤區塊修改，加快整體專案除錯的時間。      

完整測試專案連結:  
https://github.com/yuhsiang237/csharp-unit-test  

### 範例
使用Moq套件的Mock建立假的物件，並且設定函式、屬性、返回的值，再使用xUnit提供的驗證方法進行測試。

首先建立我們要測試的介面與類別，一個是IFoo.cs、Bar.cs。

**csharp-unit-test/UnitTestProject/Bar.cs**
```csharp
using System;
using System.Collections.Generic;
using System.Text;

namespace UnitTestProject
{

    public class Bar
    {
        public virtual bool Submit() { return false; }
    }

}
```

**csharp-unit-test/UnitTestProject/IFoo.cs**
```csharp
using System;
using System.Collections.Generic;
using System.Text;

namespace UnitTestProject
{
    public interface IFoo
    {
        Bar Bar { get; set; }
        string Name { get; set; }
        int Value { get; set; }
        bool DoSomething(string value);
    }

}
```

再來建立測試，使用Moq、Xunit。
我們可以對函式返回值、假物件、屬性進行測試。 
以下為測試範例: 
**/csharp-unit-test/UnitTestProjectTests/UnitTest.cs**
```csharp
using System;

using Xunit;
using Moq;

using UnitTestProject;

namespace UnitTestProjectTests
{
    /// <summary>
    /// xUnit單元測試，並使用Moq的Mock
    /// 參考資料:
    /// https://github.com/Moq/moq4/wiki/Quickstart#verification
    /// https://vimsky.com/examples/detail/csharp-ex---Mock-VerifySet-method.html
    /// https://codertw.com/%E5%89%8D%E7%AB%AF%E9%96%8B%E7%99%BC/206352/
    /// </summary>
    public class UnitTest
    {
        /// <summary>
        /// 方法單元測試
        /// </summary>
        [Fact]
        public void MethodsTest()
        {
            // 準備 Mock IFoo 介面
            var mock = new Mock<IFoo>();
            // 配置準備模擬的方法，當呼叫介面中的 DoSomething 方法，並傳遞引數 "bing" 的時候，返回 true
            mock.Setup(foo => foo.DoSomething("ping")).Returns(true);
            // 現在，你可以: 
            IFoo foo = mock.Object;
            // 檢查使用時返回值為True
            Assert.True(foo.DoSomething("ping"));
        }
        /// <summary>
        /// 屬性單元測試
        /// </summary>
        [Fact]
        public void PropertiesTest()
        {
            // 準備 Mock IFoo 介面
            var mock = new Mock<IFoo>();
            // 開始 "tracking" 屬性的 sets/gets 
            mock.SetupProperty(f => f.Name);
            // 提供一個預設的值
            mock.SetupProperty(f => f.Name, "foo");
            // 現在，你可以: 
            IFoo foo = mock.Object;
            // 儲存的值 
            Assert.Equal("foo", foo.Name);
            // 重新設定一個值
            foo.Name = "bar";
            Assert.Equal("bar", foo.Name);
        }
    }
}
```
再來開啟VisualStudio的測試視窗運行，可以得到以下結果:
{% asset_img "output.png" %}
表示測試成功。

### 總結
撰寫測試能夠改善平常除錯的速度，特別當專案大起來時。   
此外有些平台有測試的功能(如Azure DevOps Server的TFS)，可以在CI自動整合時跑測試。   

### 參考資料
- https://github.com/Moq/moq4/wiki/Quickstart#verification
- https://vimsky.com/examples/detail/csharp-ex---Mock-VerifySet-method.html
- https://codertw.com/%E5%89%8D%E7%AB%AF%E9%96%8B%E7%99%BC/206352/

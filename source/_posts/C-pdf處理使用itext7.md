---
title: C# pdf處理使用itext7
date: 2022-02-11 17:58:22
categories:
  - [程式語言,C#]
---
在這篇文章中介紹如何用C#產生PDF檔案。
使用套件為IText7。


IText7:https://itextpdf.com/en/products/itext-7/itext-7-core
程式碼範例:https://github.com/yuhsiang237/csharp-notes/tree/master/IText7Sample


先用Nuget安裝:
```
Install-Package itext7
```

如下，以下為產生一個PDF檔案，並加入標題。
其中標題為中英文混雜。

**Program.cs**
```csharp
using iText.Kernel.Font;
using iText.Kernel.Pdf;
using iText.Layout;
using iText.Layout.Element;
using iText.Layout.Properties;
using System;

namespace IText7Sample
{
    public class Program
    {
        public static void Main()
        {
            PdfFont sysFont = PdfFontFactory.CreateFont(Environment.CurrentDirectory + "/src/fonts/NotoSansCJKtc-Regular.otf", iText.IO.Font.PdfEncodings.IDENTITY_H);

            // Must have write permissions to the path folder
            PdfWriter writer = new PdfWriter(@"demo.pdf");
            PdfDocument pdf = new PdfDocument(writer);
            Document document = new Document(pdf);
            Paragraph header = new Paragraph("標題 HEADER")
               .SetFont(sysFont)
               .SetTextAlignment(TextAlignment.CENTER)
               .SetFontSize(20);

            document.Add(header);
            document.Close();
        }
    }
}
```

### 總結
可以使用此套件來快速產生PDF，如果有中文需求可以像我上面那樣自己去補中文字型即可。

### 參考資料
- https://itextpdf.com/en/products/itext-7/itext-7-core
- https://github.com/yuhsiang237/csharp-notes/tree/master/IText7Sample
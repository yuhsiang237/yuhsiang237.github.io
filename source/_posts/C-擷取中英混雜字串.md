---
title: C#擷取中英混雜字串
date: 2022-02-11 09:00:26
categories:
  - [程式語言,C#]
---
在這篇文中紀錄如何擷取中英混雜的子字串。

以下是我想到的作法，就是利用big5中文兩字元、英文一字元的特性去做。

```csharp
using System;
using System.Text;

namespace SubEnglishChineseString
{
    public static class Program
    {
        /// <summary>
        /// C#中擷取某長度中英混雜字串範例
        /// </summary>
        /// <param name="args"></param>
        static void Main(string[] args)
        {
            string str = "TESTWORD測試文字TEST";
            Console.WriteLine(SubEnglishChineseString(str,1,15));
        }
        /// <summary>
        /// C#中擷取某長度中英混雜字串
        /// </summary>
        /// <param name="str"></param>
        /// <param name="start"></param>
        /// <param name="length"></param>
        /// <returns></returns>
        public  static string SubEnglishChineseString(string str,int start,int length)
        {
            Encoding.RegisterProvider(CodePagesEncodingProvider.Instance);
            byte[] subByteArray = SubByteArray(Encoding.GetEncoding("big5").GetBytes(str), start, length);
            return Encoding.GetEncoding("big5").GetString(subByteArray);
        }
        /// <summary>
        /// C#中取得某長度byte陣列結果
        /// </summary>
        /// <param name="byteArray"></param>
        /// <param name="start"></param>
        /// <param name="length"></param>
        /// <returns></returns>
        public static byte[] SubByteArray(byte [] byteArray,int start,int length) 
        {
            byte[] resultArray = new byte[length];
            for(int i=start,j=0;i<start+length;i++,j++)
            {
                resultArray[j] = byteArray[i];
            }
            return resultArray;
        }
    }
}
```

專案存庫:https://github.com/yuhsiang237/csharp-notes/tree/master/SubEnglishChineseString

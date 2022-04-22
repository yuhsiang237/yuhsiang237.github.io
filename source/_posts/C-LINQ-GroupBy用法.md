---
title: C# LINQ GroupBy用法
date: 2022-04-22 16:32:44
categories:  
  - [Code,C#]
---
### 前言
最近很常使用GroupBy去處理資料，因此稍作整理。  
GroupBy主要用來分組，以"群"、"組"有關的都可以使用他。

### 實作
目標:使用GroupBy將每個班級的人數列出。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

namespace linq_groupby
{
    internal class Program
    {
        public class Student
        {
            /// <summary>
            /// 編號
            /// </summary>
            public int Id { get; set; }

            /// <summary>
            /// 姓名
            /// </summary>
            public string Name { get; set; }

            /// <summary>
            /// 教室
            /// </summary>
            public char Classroom { get; set; }
        }

        static void Main(string[] args)
        {
            var students = new List<Student>
            {
                new Student {Id = 1, Name = "大雄",Classroom = 'A'},
                new Student {Id = 2, Name = "多拉A夢",Classroom = 'B'},
                new Student {Id = 3, Name = "胖虎",Classroom = 'A'},
            };
            var classRoomGroup = students
                .GroupBy(x => x.Classroom)
                .Select(x => new
                {
                    ClassRoom = x.Key,
                    StudentCount = x.Count(),
                }).ToList();

            foreach (var item in classRoomGroup)
            {
                Console.WriteLine($"Classroom:{item.ClassRoom} - StudentCount:{item.StudentCount}");
            }
        }
    }
}

```

輸出結果:  
```
Classroom:A - StudentCount:2
Classroom:B - StudentCount:1
```
A班級2人，B班級1人。  

### 總結
使用LINQ GroupBy能夠節省建立資料結構如字典、HashSet等，直接去做分組統計。

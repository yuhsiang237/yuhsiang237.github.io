---
title: 'C# Use AutoMapper to map data model'
date: 2023-03-27 11:18:27
categories:
  - [Code,C#] 
---
When writing data to SQL Server, there is always a process about mapping model.
The process is a transformation, such as A model transforming B model.
If the model contains too many properties, the code could became very long.
So we can use a plugin, AutoMapper, to avoid that.

AutoMapper : https://docs.automapper.org/

The following is an example of implementation about converting PersonModel to PersonDto, which is a list convert.

```csharp
using System;
using System.Collections.Generic;

using AutoMapper;

namespace AutoMapExample
{
    public class Program
    {
        public class PersonModel
        {
            public string PersionalID { get; set; }
            public string Name { get; set; }
            public int? Age { get; set; }
        }
        public class PersonDto
        {
            public int? Id { get; set; }
            public string Pid { get; set; }
            public string Name { get; set; }
            public int? Age { get; set; }
        }
        public static void Main()
        {
            var personlist = new List<PersonModel>
            {
                new PersonModel
                {
                    PersionalID = "E111111111",
                    Name = "TOM",
                    Age = 20,
                },
                new PersonModel
                {
                    PersionalID = "B111111111",
                    Name = "J.Cole",
                    Age = 26,
                }
            };

            var config = new MapperConfiguration(cfg =>
            {
                cfg.CreateMap<PersonModel, PersonDto>()
                    .ForMember(dest => dest.Pid, opt => opt.MapFrom(src => src.PersionalID))
                    .ForMember(dest => dest.Id, opt => opt.Ignore());
            });

            // Check for missing mapped properties.
            config.AssertConfigurationIsValid();

            var mapper = config.CreateMapper();
            var dtoList = mapper.Map<List<PersonModel>, List<PersonDto>>(personlist);

            dtoList.ForEach(x =>
                {
                    Console.WriteLine($"id:{x.Id},pid:{x.Pid},name:{x.Name},age:{x.Age}");
                });
        }
    }
}
```

**output**
```
id:,pid:E111111111,name:TOM,age:20
id:,pid:B111111111,name:J.Cole,age:26
```

Because id was set to ignore, it's null when the console prints them. 

The following is the mapping of the different name properity. Due to The model's properity is different so I have to define a rule to map, such as PersionalID => Pid.
```csharp
ForMember(dest => dest.Pid, opt => opt.MapFrom(src => src.PersionalID))
```
The following is a check for missing mapped properties.
```csharp
config.AssertConfigurationIsValid()
```
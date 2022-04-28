---
title: PostgreSQL INSERT INTO MULTIPLE
date: 2022-04-28 10:26:17
categories:
  - [Database,PostgreSQL]
---
### 前言
在SQL有時需要手動建立測資，但又不想逐行insert into時可以使用。

語法:
```sql
INSERT INTO table_name (column_list)
VALUES
    (value_list_1),
    (value_list_2),
    ...
    (value_list_n);
```

舉例:
```sql
INSERT INTO USER
(
    NAME,
    BIRTHDAY
)
VALUES
    ('胖虎','1993-1-1'),
    ('小夫','1993-2-1')
```
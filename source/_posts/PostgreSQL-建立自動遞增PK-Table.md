---
title: PostgreSQL 建立自動遞增PK Table
date: 2022-03-17 10:26:17
categories:
  - [Database,PostgreSQL]
---
### 前言
因為發現用PGADMIN建立Table速度太慢(超久的Loading)，因此最後就習慣用指令去建立Table了。
以下為在PostgreSQL建立一個遞增+1的SEQ欄位，且為Table的PK。

```sql
CREATE TABLE (
    SEQ SERIAL PRIMARY KEY,
    NAME CHARACTER VARYING(100) NULL,
    REMARK TEXT,
    IS_VALID BOOLEAN NULL,
    CREATED_ON TIMESTAMP WITHOUT TIME ZONE NULL,
    UPDATED_ON TIMESTAMP WITHOUT TIME ZONE NULL,
)
```

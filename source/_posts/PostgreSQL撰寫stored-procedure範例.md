---
title: PostgreSQL撰寫stored procedure範例
date: 2022-03-04 13:38:19
categories:
  - [Database,PostgreSQL]
---
### 前言
在這篇中介紹如何撰寫一個PostgreSQL的stored procedure。

### 實作
**目標:**
撰寫一個傳送用戶ID回傳用戶資料的procedure。

以下是一個固定的模板，我們將以此模板來撰寫。
~~~~~sql
create [or replace] procedure procedure_name(parameter_list)
language plpgsql
as $$
declare
-- variable declaration
begin
-- stored procedure body
end; $$
~~~~~
- procedure_name : 是prodcedure的名子
- parameter_list : 放傳進參數。可以選擇兩種方式:inout、out
- plpgsql : 使用的prodcedure內的語法，可以改sql或C等等，看自己用哪種方式寫
- $$:可以看到有被各一個$$圍住的區塊就是body，寫語法的區塊就在$$內。

補充:
stored prodcedure沒有回傳值，所以並不會有return expression;的寫法，但你可以加return;讓程式執行到一半停止執行。

**實際撰寫**

users資料表:
{% asset_img "1.PNG" %}

因為PostgreSQL的stored procedure沒有回傳值，所以就要用參考的方式去拿值。
以下用INOUT refcursor refcursor去當作參考。
IN user_id integer則是要取得資料的使用者id。

~~~~~sql
CREATE OR REPLACE PROCEDURE get_user_data
(
	IN user_id integer,
	INOUT refcursor refcursor
) 
LANGUAGE 'plpgsql'
AS $$
DECLARE
  str_sql varchar;                                                    
BEGIN
  str_sql := 'SELECT * FROM users WHERE id = '|| user_id ;
  OPEN refcursor FOR EXECUTE str_sql;                             
END;
$$
~~~~~
- || : 是字串的連結符號
傳進一個user_id跟參考的物件。

建好後可在左方PGADMIN看到
{% asset_img "4.PNG" %}


之後可以用call來執行。
建立一個區域，宣告一個RECORD物件用來印資料、一個refcursor物件用來參考
接著跑loop迴圈，將每個out_ref放進rcd，再用raise notice印出。
如果沒資料則用IF判斷式離開。

~~~~~sql
DO $$
DECLARE
	rcd RECORD;
	out_ref refcursor;
BEGIN
	call get_user_data(2,out_ref);
	loop
		fetch in out_ref into rcd;
		IF NOT FOUND THEN
			EXIT;
		END IF;
		raise notice 'user_id=%', rcd.id;
		raise notice 'user_name=%', rcd.name;
  	end loop;
	close out_ref;
END;
$$ LANGUAGE plpgsql;
~~~~~
結果:
{% asset_img "3.PNG" %}


### 總結
PostgreSQL的prodcedure並不好寫，因為他沒辦法直接有返回值。要用參考的方式。他不像SQL Server一樣簡單，能直接Select完畢，因為並不具備回傳值。要回傳值就得寫他的function，就不是prodcedure了。

然後，如果想參考SQL Server的prodcedure我之前也有寫一篇:{% post_link 'SQL-Server-Stored-Procedure實作範例' '
SQL-Server-Stored-Procedure實作範例' %}可以參考。

### 參考資料
- https://www.postgresqltutorial.com/postgresql-create-procedure/
- https://www.sqlines.com/postgresql/how-to/return_result_set_from_stored_procedure
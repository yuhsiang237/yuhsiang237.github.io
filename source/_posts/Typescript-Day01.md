---
title: '[Day01] Typescript與Javascript差異'
date: 2025-08-11 21:17:12
categories:
  - [Topic,2025 Typescript]
  - [Code,Javascript]
  - [Code,Typescript]
---

因為未來工作長期會使用到Typescript，所以就趁這時候筆記一下與JS的差異方面。
過去在寫Vue3、Angular時有大量接觸Typescript，就趁這時間重溫一下。

1. 賦值

JS 是動態型別 → 錯誤在執行時才發現
TS 是靜態型別 → 編譯時就檢查出錯，減少 bug

JS
```javascript
let age = 25;
age = "twenty-five"; // ✅ JS 不會報錯，可能導致執行期錯誤
```
TS
```typescript
let age: number = 25;
age = "twenty-five"; 
// ❌ 編譯錯誤：Type 'string' is not assignable to type 'number'
```

2. 函數參數與回傳型別
TS 允許定義 參數型別 和 回傳型別，防止不合法輸入

TS
```typescript
function add(a: number, b: number): number {
  return a + b;
}
add(1, "2"); 
// ❌ Argument of type 'string' is not assignable to parameter of type 'number'
```

3.可選參數及預設值

TS 用 ? 宣告參數可選
搭配 nullish coalescing (??) 處理 null 或 undefined

```typescript
function greet(name?: string) {
  console.log(`Hello, ${name ?? "Guest"}`);
}
greet();  // Hello, Guest
```

JS則是用||處理

JS
```javascript
function greet(name) {
  console.log(`Hello, ${name || "Guest"}`);
}
greet(); // Hello, Guest
```

4.Interface 

TS interface 能保證物件結構正確，JS 則只能在執行時才發現問題

TS
```typescript
interface User {
  name: string;
  age: number;
}

function printUser(user: User) {
  console.log(user.name, user.age);
}
printUser({ name: "Alex", age: 25 }); // ✅
printUser({ name: "Alex" });
// ❌ Property 'age' is missing in type ...
```

5.Enum vs. Magic String

Enum 可集中管理常量並提供型別檢查，避免拼錯問題
TS
```typescript
enum Status {
  Success = "success",
  Fail = "fail"
}

const status: Status = Status.Success;

if (status === Status.Success) {
  console.log("OK");
}
```

JS則無
JS
```javascript
const status = "success"; // Magic string，容易拼錯
if (status === "succes") { // 拼錯不會報錯
  console.log("OK");
}
```

6.參數多種型別檢查Union Types

TS:
```typescript
function printId(id: number | string) {
  console.log(id);
}
printId(123);
printId("abc");
printId(true); 
// ❌ Argument of type 'boolean' is not assignable to parameter of type 'string | number'
```

7. 泛型 (Generics)

any 會失去型別資訊
泛型保留型別，讓函數既安全又通用
TS:
```typescript
function identity<T>(arg: T): T {
  return arg;
}

const a = identity(123); // a: number
const b = identity("hi"); // b: string
```

一般JS不好確認型別
JS:
```javascript
function identity(arg) {
  return arg; // 無法知道回傳型別
}
```

8.非空斷言與 Optional Chaining

?. 可安全存取深層屬性
搭配 ! 非空斷言在確定不為空時跳過檢查
TS:
```typescript
const user: any = {};
console.log(user?.profile?.name); // ✅ undefined
```

JS則無法
JS:
```javascript
const user = {};
console.log(user.profile.name); // ❌ TypeError
```

9. any vs. unknown
什麼時候用 any，什麼時候用 unknown？


JS:
```javascript
let value; // 無型別限制
value.foo(); // 可能出錯
```

any 跟 JS 一樣完全不檢查型別（危險）
unknown 要先檢查型別才可使用（安全）

TS:
```typescript
let value: unknown;
value = "hello";
// value.toUpperCase(); ❌ 必須先做型別檢查

if (typeof value === "string") {
  console.log(value.toUpperCase()); // ✅
}
```

10. 類別 (Class) 的型別修飾符

JS 與 TS 在類別屬性可見性上的差異
JS:
```javascript
class Person {
  constructor(name) {
    this.name = name; // 無法限制外部訪問
  }
}
```

TS 提供 public（預設）、private、protected，可精確控制存取範圍


TS:
```typeScript
class Person {
  private name: string; // 僅類別內可訪問
  constructor(name: string) {
    this.name = name;
  }
}
```

11. 型別別名（Type Alias）與複雜型別組合

大型專案中可以將複雜型別命名，方便重用與維護
TS:
```typescript
type OrderStatus = "pending" | "shipped" | "delivered";
type Order = { id: number; status: OrderStatus };

function process(order: Order) { /* ... */ }
```

12. Intersection Types（交叉型別）合併資料結構

JS:
```javascript
// 只能動態合併物件
const person = Object.assign({}, { name: "Alex" }, { age: 25 });
```

交叉型別在多個模組資料合併時很常用（特別是 Redux / API 結果整合）

TS:
```typescript
type Name = { name: string };
type Age = { age: number };
type Person = Name & Age; // 交叉型別

const person: Person = { name: "Alex", age: 25 };
```

13. 型別守衛（Type Guards）

JS
```javascript
function handle(val) {
  if (val.start) val.start(); // 可能 undefined
}
```


大型專案裡經常用自訂型別守衛來精準縮小型別範圍

TS
```typescript
function isFunction(value: unknown): value is Function {
  return typeof value === "function";
}

function handle(val: unknown) {
  if (isFunction(val)) {
    val(); // ✅ 已縮小型別
  }
}
```

14. 宣告檔（.d.ts）與第三方函式庫型別

JS:
```javascript
// 使用第三方庫時，無法獲得型別提示
import moment from "moment";
moment().format("YYYY-MM-DD"); // 沒 IntelliSense
```

大型專案幾乎都要用 .d.ts 來補齊沒有型別定義的第三方套件
TS:
```typescript
// @types/moment 提供型別定義
import moment from "moment";
moment().format("YYYY-MM-DD"); // ✅ 型別提示 + 自動完成
```

15.readonly 與 Immutable 資料結構

readonly 對大型專案的設定檔、常量物件非常重要

Typescript
```typescript
type Config = {
  readonly port: number;
};

const config: Config = { port: 3000 };
config.port = 4000; 
// ❌ Cannot assign to 'port' because it is a read-only property
```

16. keyof 與型別安全的物件鍵名存取

JS
```javascript
function getProp(obj, key) {
  return obj[key]; // key 可能錯拼
}
```

keyof 確保傳入的屬性名稱是物件中真實存在的鍵名


TS
```typescript
function getProp<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}

const user = { id: 1, name: "Alex" };
getProp(user, "name"); // ✅
getProp(user, "age");  
// ❌ Argument of type '"age"' is not assignable to parameter of type '"id" | "name"'
```
17. Mapped Types（映射型別）

JS:
```javascript
// 只能手動定義每個屬性為可選
const partialUser = { name: "Alex" };
```
大型專案中 Partial、Required、Readonly 等都是 Mapped Types 的應用

TS:
```typescript
type User = { id: number; name: string; age: number };
type PartialUser = { [K in keyof User]?: User[K] };

const partialUser: PartialUser = { name: "Alex" };
```

18. 模組與命名空間（Modules vs Namespaces）

JS:
```javascript
// 無型別檢查的全域污染
window.myApp = {};
```
大型專案建議使用 ES Modules 搭配 TS 的型別系統管理命名與依賴

TS:
```typescript
// 使用模組系統
export const myApp = {};
```

19. 型別推斷與 as const

```javascript
const roles = ["admin", "user"];

```
as const 在定義固定字面值陣列、物件時非常有用（例如權限、狀態碼）

```typescript
const roles = ["admin", "user"] as const;
// roles: readonly ["admin", "user"]
type Role = typeof roles[number]; // "admin" | "user"
```

20. Non-null Assertion（非空斷言）與嚴格模式

JS:
```javascript
let el = document.getElementById("myDiv");
el.style.color = "red"; // el 可能是 null
```
嚴格模式 (strictNullChecks) 在大型專案中很重要，可以提早發現 null/undefined 錯誤


TS:
```typescript
let el = document.getElementById("myDiv")!;
el.style.color = "red"; // ✅ 告訴 TS 這裡一定不為 null
```
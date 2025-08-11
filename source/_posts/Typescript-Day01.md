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
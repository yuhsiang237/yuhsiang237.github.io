---
title: '[Day03] Typescript面試選擇30題'
date: 2025-08-13 21:17:12
categories:
  - [Topic,2025 Typescript]
  - [Code,Javascript]
  - [Code,Typescript]
---
來到第三天，這回用AI生成了常見的Typescript面試選擇題。
一共30題，現在使用AI確實比過往一個一個爬stackoverflow好很多了。

涵蓋基礎、進階、型別系統與實務應用

---

## **TypeScript 面試選擇題（共 30 題）**

### **基礎語法與型別**

1. **以下哪一個是正確的 TypeScript 型別宣告？**
   A. `let age: number = '25';`
   B. `let age: string = 25;`
   C. `let age: number = 25;` ✅
   D. `let age: any = 25;` 

   **解析**：`number` 不能賦值為字串，C 正確。D:（語法正確，但不建議） 型別標註 any，可以接受任何型別，但失去型別檢查的優勢。很多 TypeScript 規範會禁止 any

---

2. **`unknown` 型別的特性是什麼？**
   A. 可以直接做任何運算
   B. 必須先進行型別檢查後才能使用 ✅
   C. 與 `any` 完全相同
   D. 只能賦值為 `null`

   **解析**：`unknown` 比 `any` 更安全，需要型別收窄後才能操作。
    unknown = "我不知道它是什麼型別，所以你得先確認再用"
    any = "我懶得檢查，全部放行"

```typescript
    let u: unknown = "Hello";

// 錯誤：不能直接調用方法
// console.log(u.toUpperCase()); ❌

// 必須檢查型別
if (typeof u === "string") {
  console.log(u.toUpperCase()); // ✅
}

// 或使用型別斷言
console.log((u as string).toUpperCase()); // ✅
    ```
---

3. **哪一個關鍵字用來定義常數 enum？**
   A. `enum`
   B. `const enum` ✅
   C. `static enum`
   D. `readonly enum`

   **解析**：`const enum` 在編譯時會被內聯，效能更好。

---

4. **下面哪種是交叉型別（Intersection Types）的正確語法？**
   A. `type Person = { name: string } && { age: number };`
   B. `type Person = { name: string } | { age: number };`
   C. `type Person = { name: string } & { age: number };` ✅
   D. `type Person = { name: string, age: number };`

   **解析**：交叉型別用 `&`，代表合併兩個型別。

---

5. **下列哪個是泛型函式的正確定義？**
   A. `function identity<T>(arg: T): T { return arg; }` ✅
   B. `function identity(arg: T): T { return arg; }`
   C. `function identity<T>(T arg) { return arg; }`
   D. `function identity(arg) { return arg; }`

---

### **進階型別系統**

6. **`Partial<T>` 工具型別的功能是什麼？**
   A. 把 T 所有屬性變成必填
   B. 把 T 所有屬性變成可選 ✅
   C. 移除 T 的所有屬性
   D. 把 T 轉換成陣列

---

7. **下列哪一個是索引型別查詢的正確用法？**
   A. `type Keys = keyof Person;` ✅
   B. `type Keys = valueof Person;`
   C. `type Keys = indexof Person;`
   D. `type Keys = typeof Person;`

等同於

```typescript
type Keys = keyof Person;
// 等同於：type Keys = "name" | "age" | "address"

```
---

8. **`never` 型別的用途是什麼？**
   A. 表示一個函式永遠不會回傳 ✅
   B. 表示一個變數可以是任何型別
   C. 表示一個變數為空
   D. 表示一個變數尚未初始化

---

9. **下列哪個範例正確使用 `as const`？**
   A. `const arr = [1, 2, 3] as const;` ✅
   B. `let arr = [1, 2, 3] as const;`
   C. `const arr as const = [1, 2, 3];`
   D. `const arr = as const [1, 2, 3];`

as const 的作用是把一個值斷言為最窄的型別（literal type），而且會把它變成 readonly。
元素型別不再是 number，而是字面值型別 → 第一個元素就是 1、第二個是 2、第三個是 3

陣列變成唯讀（readonly） → 不能改值或 push
```typescript
arr[0] = 99; // ❌ 錯誤：Cannot assign to '0' because it is a read-only property
arr.push(4); // ❌ 錯誤：Property 'push' does not exist on type 'readonly [1, 2, 3]'
```
---

10. **哪一種方式可以防止物件被修改？**
    A. `readonly` ✅
    B. `private`
    C. `const`
    D. `static`

const → 只能防止變數的重新指派，但物件內容還是可以改：
```typescript
const obj = { name: "Tom" };
obj.name = "Jerry"; // ✅ 可以改
```
readonly 可用在物件屬性上（interface 或 type）、類別屬性上
```typescript
interface User {
  readonly id: number;
  name: string;
}

const user: User = { id: 1, name: "Alice" };
user.id = 2; // ❌ 錯誤，readonly 屬性不能改
user.name = "Bob"; // ✅ 可以改
```
---

### **函式與 OOP**

11. **TypeScript 中，介面（interface）可以**
    A. 只用來定義物件形狀 ✅
    B. 被類別實作 ✅
    C. 被擴展（extend） ✅
    D. 以上皆是 ✅

---

12. **函式的可選參數應該放在**
    A. 最前面
    B. 中間
    C. 最後面 ✅
    D. 任意位置

最後面
```typescript
// 正確範例：可選參數放最後面
function greet(name: string, age?: number) {
  if (age !== undefined) {
    console.log(`Hello ${name}, you are ${age} years old.`);
  } else {
    console.log(`Hello ${name}`);
  }
}

greet("Alice");         // OK
greet("Bob", 30);       // OK
```
---

13. **下列哪個範例正確定義函式多載（Overload）？**
    A.✅

    ```ts
    function add(a: number, b: number): number;
    function add(a: string, b: string): string;
    function add(a: any, b: any) { return a + b; }
    ``` 

    B.  
    ```ts
    function add(a, b) { return a + b; }
    ```

    C.

    ```ts
    function add(a: number | string, b: number | string) { return a + b; }
    ```

    D. 以上皆非


函式多載（Function Overloading）是指在同一個函式名稱底下，定義多種不同的參數型態與數量，讓函式能根據呼叫時傳入的參數型別或數量，執行不同的邏輯或回傳不同型別的結果。

---

14. **`abstract` 關鍵字的用途是什麼？**
    A. 定義不能被實例化的類別 ✅
    B. 定義常數
    C. 定義不可修改的變數
    D. 定義泛型

---

15. **以下哪個是介面擴展的正確方式？**
    A. `interface B extends A {}` ✅
    B. `interface B implements A {}`
    C. `class B extends A {}`
    D. `type B = A extends {}`

```ts
// 先定義介面 A
interface A {
  name: string;
  age: number;
}

// B 繼承 A，新增一個屬性 job
interface B extends A {
  job: string;
}

// 使用範例
const person: B = {
  name: "Alice",
  age: 30,
  job: "Engineer",
};

console.log(person.name); // Alice
console.log(person.job);  // Engineer
```
---

### **實務應用與錯誤檢查**

16. **TypeScript 中 `strictNullChecks` 開啟後會**
    A. 禁止變數為 `null` 或 `undefined` 除非型別允許 ✅
    B. 自動轉換 `null` 為空字串
    C. 忽略 `null`
    D. 允許任何型別

---

17. **`tsconfig.json` 中 `noImplicitAny` 的作用是**
    A. 允許隱含 `any`
    B. 禁止隱含 `any` ✅
    C. 自動推斷 `any`
    D. 強制使用 `unknown`

---

18. **下列哪一種是型別守衛（Type Guard）？**
    A. `typeof value === 'string'` ✅
    B. `console.log(value)`
    C. `value.length`
    D. `value instanceof` ✅

型別守衛（Type Guard）是 TypeScript 裡用來在程式碼執行時檢查變數型別的一種技術。它可以讓編譯器根據條件判斷，縮小變數的型別範圍，從而安全地訪問該型別的屬性或方法。
typeof value === 'string'
利用 typeof 運算子判斷原始型別，常用於檢查 string、number、boolean 等基本型別，是一種典型的型別守衛。
value instanceof SomeClass
利用 instanceof 判斷物件是否是某個類別的實例，也是典型的型別守衛。
---

19. **哪個工具型別會移除指定屬性？**
    A. `Pick<T, K>`
    B. `Omit<T, K>` ✅
    C. `Partial<T>`
    D. `Exclude<T, K>`
這四個都是 TypeScript 內建的 工具型別（Utility Types），用來操作和轉換型別，讓開發更方便靈活。

```ts
//1.
interface Person {
  name: string;
  age: number;
  address: string;
}

type PersonNameAge = Pick<Person, "name" | "age">;
// 等同於 { name: string; age: number; }

//2.
type PersonWithoutAddress = Omit<Person, "address">;
// 等同於 { name: string; age: number; }

//3.
type PartialPerson = Partial<Person>;
// 等同於 { name?: string; age?: number; address?: string; }

//4.
type T1 = "a" | "b" | "c";
type T2 = Exclude<T1, "a" | "b">;
// 結果是 "c"

```
---

20. **下列哪個是 `enum` 的預設值？**
    A. 從 0 開始遞增 ✅
    B. 從 1 開始遞增
    C. 必須手動指定
    D. 沒有預設值

---

### **混合進階題**

21. **`infer` 關鍵字用於**
    A. 在條件型別中推斷型別 ✅
    B. 宣告泛型
    C. 定義類別
    D. 宣告常數

infer 是 TypeScript 的關鍵字，只能用在 條件型別（conditional types） 裡面，
用來 從型別中「推斷（infer）」某個子型別，然後讓你在條件型別的「真分支」使用這個推斷出來的型別。
```ts
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

function fn() {
  return 123;
}

type Result = ReturnType<typeof fn>; // Result = number
```
---

22. **哪個選項正確使用了條件型別？**
    A. `type IsString<T> = T extends string ? true : false;` ✅
    B. `type IsString<T> = string extends T ? true : false;`
    C. `type IsString<T> = T ? true : false;`
    D. 以上皆非

---

23. **下列哪個型別表示「除了 T 以外的所有型別」？**
    A. `Exclude<U, T>` ✅
    B. `Extract<U, T>`
    C. `Omit<U, T>`
    D. `Partial<T>`

---

24. **以下程式碼輸出什麼？**

    ```ts
    enum Color { Red, Green, Blue }
    console.log(Color[0]);
    ```

    A. `0`
    B. `'Red'` ✅
    C. `'0'`
    D. `undefined`

---

25. **TypeScript 編譯器的輸出是什麼？**
    A. `.ts` 檔案
    B. `.d.ts` 檔案
    C. `.js` 檔案 ✅
    D. `.tsconfig` 檔案

---

26. **哪個修飾子可以讓類別屬性在繼承中可用，但在外部不可存取？**
    A. `private`
    B. `protected` ✅
    C. `public`
    D. `readonly`

---

27. **下列哪個不是型別別名（Type Alias）的用途？**
    A. 為複雜型別取別名
    B. 定義物件形狀
    C. 建立聯合型別
    D. 被類別實作 ✅

---

28. **TypeScript 中模組預設匯出的正確寫法是**
    A. `export = MyClass;`
    B. `export default MyClass;` ✅
    C. `module.exports = MyClass;`
    D. `exports.MyClass = MyClass;`

---

29. **下列哪個是非同步函式的正確寫法？**
    A. `function getData(): Promise<string> {}`
    B. `async function getData(): Promise<string> {}` ✅
    C. `function getData(): string {}`
    D. `await function getData() {}`

```ts
// 非同步函式，回傳 Promise<string>
async function getData(): Promise<string> {
  // 模擬非同步操作，例如從伺服器取得資料
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve("這是取得的資料");
    }, 1000);
  });
}

// 使用範例
async function main() {
  console.log("開始取得資料...");
  const data = await getData();
  console.log("取得資料:", data);
}

main();
```

---

30. **TypeScript 型別檔案的副檔名是**
    A. `.ts`
    B. `.js`
    C. `.d.ts` ✅
    D. `.type.ts`

.d.ts 檔案（宣告檔）
只包含型別宣告（Type Declarations），不包含實作。
.ts 檔案
定義程式碼的實作，包含函式、類別、變數等。
是你寫邏輯和功能的主要地方。
編譯時會被轉成 JavaScript。
---
補充複選題：
以下哪些是 TypeScript 中內建的型別（Built-in Types）？（可複選，並說明它們的用途）

好，我幫你把剛剛那題複選題改成面試題完整格式，
並且在每個正確答案後面加上 **使用場景說明**，方便記憶與考試時快速回想。

---

**題目：**
以下哪些是 **TypeScript 中內建的型別（Built-in Types）**？（可複選，並說明它們的用途）

---

A. `string` ✅
　用來表示文字資料，例如：
　`ts
　let name: string = "Alice";
　`

B. `number` ✅
　用來表示數字（整數與浮點數），例如：
　`ts
　let age: number = 25;
　`

C. `boolean` ✅
　表示布林值（true/false），例如：
　`ts
　let isActive: boolean = true;
　`

D. `symbol` ✅
　表示唯一且不可變的值（常用於物件屬性 key），例如：
　`ts
　const id: symbol = Symbol("id");
　`

E. `bigint` ✅
　表示任意精度的整數（大於 `Number.MAX_SAFE_INTEGER` 時使用），例如：
　`ts
　let big: bigint = 9007199254740991n;
　`

F. `null` ✅
　表示空值（需在 `--strictNullChecks` 關閉時能直接賦值），例如：
　`ts
　let nothing: null = null;
　`

G. `undefined` ✅
　表示未定義的值，變數未賦值時的預設值，例如：
　`ts
　let notAssigned: undefined = undefined;
　`

H. `void` ✅
　表示函式不返回值，例如：
　`ts
　function logMessage(): void {
　  console.log("Hello");
　}
　`

I. `never` ✅
　表示永遠不會有返回值（例如函式會拋出錯誤或無限循環），例如：
　`ts
　function throwError(): never {
　  throw new Error("Error!");
　}
　`

J. `object` ✅
　表示非原始型別的值，例如物件、陣列、函式等：
　`ts
　let person: object = { name: "Bob" };
　`

K. `any` ✅
　表示不檢查型別（會失去型別安全），例如：
　`ts
　let data: any = 123;
　data = "Hello";
　`

L. `unknown` ✅
　表示未知型別（比 `any` 安全，必須先檢查型別才能使用），例如：
　`ts
　let value: unknown = "Hello";
　if (typeof value === "string") {
　  console.log(value.toUpperCase());
　}
　`

M. `Function` ✅
　表示任何函式型別，例如：
　`ts
　let fn: Function = () => console.log("Run");
　`

N. `Array` ✅
　表示陣列（同時可以寫成 `T[]`），例如：
　`ts
　let list: Array<number> = [1, 2, 3];
　`

O. `tuple` ✅
　表示固定長度且每個元素型別已知的陣列，例如：
　`ts
　let tuple: [string, number] = ["Age", 25];
　`

P. `Record` ❌
　（工具型別，不是基本型別）用於建立具有特定 key 與 value 型別的物件，例如：
　`ts
　type User = Record<"id" | "name", string>;
　`

Q. `enum` ✅
　列舉型別，用於定義一組具名常數，例如：
　`ts
　enum Direction { Up, Down, Left, Right }
　`

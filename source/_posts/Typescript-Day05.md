---
title: '[Day05] Typescript 取得資料後進行的處理'
date: 2025-08-15 21:17:12
categories:
  - [Topic,2025 Typescript]
  - [Code,Javascript]
  - [Code,Typescript]
---
這回主要著重在Typescript取得資料後進行的處理
主要介紹兩種處理:
1. JSON與JSON合併
2. 巢狀JSON內屬性為undefined時存取


### 實作一：JSON與JSON合併

有user、userDetail，將相同userId的資料做合併

json檔案
```javascript
// user.json
[
  { "userId": "1", "name": "LIN" },
  { "userId": "2", "name": "DES" }
]

// userDetail.json
[
  { "userId": "1", "age": 29, "city": "TAIPEI" },
  { "userId": "2", "age": 20, "city": "TAIPEI" }
]
```

typescript實作資料合併:

```typescript
interface User {
  userId: string;
  name: string;
}

interface UserDetail {
  userId: string;
  age: number;
  city: string;
}
interface UserWithDetail {
  userId: string;
  name: string;
  age: number;
  city: string;
}
const users: User[] = [
  { userId: "1", name: "LIN" },
  { userId: "2", name: "DES" },
];

const userDetails: UserDetail[] = [
  { userId: "1", age: 29, city: "TAIPEI" },
  { userId: "2", age: 20, city: "TAIPEI" },
];

// 合併
const usersWithDetails : UserWithDetail[] = users.map(user => {
  // 合併，若找不到給空 detail 符合介面屬性
  const detail = userDetails.find(d => d.userId === user.userId) || { age: 0, city: "" };;
  return { ...user, ...detail };
});

/*
users.map(...) 會遍歷 users 陣列，每個使用者跑一次 callback。
在 callback 裡，用 .find(...) 去 userDetails 陣列裡找符合的 userId。
.find() 每次可能要從頭掃到尾，最糟情況要檢查整個陣列。
最後用展開運算子 {...user, ...detail} 合併成一個新物件。
缺點:
如果 users 有 n 筆，userDetails 有 m 筆
.map() 外層跑 n 次，每次 .find() 要在 m 筆資料裡找
總時間複雜度 = O(n × m)
如果 n=10,000，m=10,000 → 需要大約 1 億次比對（非常慢）
*/


console.log(usersWithDetails);
```

改善更好作法：
typescript實作資料合併:

```ts
interface User {
  userId: string;
  name: string;
}

interface UserDetail {
  userId: string;
  age: number;
  city: string;
}

interface UserWithDetail {
  userId: string;
  name: string;
  age: number;
  city: string;
}
const users: User[] = [
  { userId: "1", name: "LIN" },
  { userId: "2", name: "DES" },
];

const userDetails: UserDetail[] = [
  { userId: "1", age: 29, city: "TAIPEI" },
  { userId: "2", age: 20, city: "TAIPEI" },
];

// 先用 users 建立 Map
// 這裡用 Partial<UserDetail> 的目的，是 允許在 Map 裡的 user 物件還沒有完整的 UserDetail 時，也能先存入 Map，而不會 TypeScript 報錯。
const userMap = new Map<string, User & Partial<UserDetail>>(
  users.map(user => [user.userId, { ...user }])
);

// 把詳細資料合併進去
userDetails.forEach(detail => {
  const user = userMap.get(detail.userId);
  if (user) {
    userMap.set(detail.userId, { ...user, ...detail });
  }
});
// 最後轉回陣列
const usersWithDetails: UserWithDetail[] = Array.from(userMap.values());

console.log(usersWithDetails);

/*
[
  { userId: '1', name: 'LIN', age: 29, city: 'TAIPEI' },
  { userId: '2', name: 'DES', age: 20, city: 'TAIPEI' }
]
*/

/*
優點：

1.用 Map 做為索引，查找效率高
把 users 陣列先轉成 Map，以 userId 當 key
查找一個 user 是否存在，只需 O(1)，比起用 users.find(...) 的 O(n) 快很多
優點：當 users 或 userDetails 很大時，效能差異非常明顯

2.直接把 detail 複製進 user 物件，簡潔明瞭
使用Map.set保持原資料不被修改（透過展開建立新物件）
適用於 Immutable 思維（對 React / Vue 等框架更安全）
可輕鬆擴展多個 detail 陣列

3.把 Map 的 value 轉成陣列，非常直觀
Array.from  時間	O(n) 空間	O(n)
不用額外 .forEach 或 .map
代碼簡潔，可讀性高
*/


```

### 實作二：巢狀JSON內屬性為undefined時存取
假設有以下資料，而某些物件數值為undefined，該如何避免runtime存取問題。

```ts
interface Address {
  type: string;
  city: string;
}

interface UserDetail {
  age: number;
  address: Address;
}

interface User {
  userId: string;
  name: string;
  userDetail?: UserDetail; // 可選 
  // userDetail? 是 可選屬性，它的型別預設就是 UserDetail | undefined
}

/*
必填欄位 → TypeScript 要求一定要給值
nullable/optional 欄位 → 可以用 ?. 或判斷安全存取
*/

const user1: User = {
  userId: "1",
  name: "LIN",
  userDetail: {
    age: 29,
    address: { type: "home", city: "TAIPEI" },
  },
};

const user2: User = {
  userId: "2",
  name: "DES",
  // userDetail: undefined // 可省，也等於undefined
};

const user1addresstype = user1.userDetail?.address?.type; 
const user2addresstype = user2.userDetail?.address?.type; // 安全，會是 undefined

```

userDetail 在 User 介面裡是 可選的 (?)
所以 user2 沒有 userDetail 也符合型別
存取時使用了 optional chaining (?.)
user1addresstype → "home"
user2addresstype → undefined（因為 userDetail 不存在

避免了必填欄位缺失的型別錯誤
安全存取巢狀欄位
如果要進一步操作 user2addresstype，需要考慮可能是 undefined

在 TypeScript/JavaScript 裡，?.（optional chaining）運作的原理就是 如果左邊是 null 或 undefined，就直接回傳 undefined 而不是繼續存取，所以不會拋錯。
後面的 .address 和 .type 不會執行 → 沒有 runtime error
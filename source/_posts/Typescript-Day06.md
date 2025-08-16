---
title: '[Day04] Typescript 排序套件庫Lodash'
date: 2025-08-14 21:17:12
categories:
  - [Topic,2025 Typescript]
  - [Code,Javascript]
  - [Code,Typescript]
---
在處理陣列物件時常會遇到需要排序陣列的問題，而用sort 得搭配 reduce，寫法醜、不好閱讀。
所以就詢問看看AI有何通用套件在大的專案裡常用。

目前是Lodash（最常用），閱讀起來也很直觀。

目前有三種

1. Lodash（最常用）

Lodash安裝
```
npm install lodash
```

單屬性排序.ts
```ts
import _ from "lodash";

interface User {
  id: number;
  name: string;
  age: number;
}

const users: User[] = [
  { id: 3, name: "Tom", age: 28 },
  { id: 1, name: "Amy", age: 22 },
  { id: 2, name: "John", age: 30 },
];

// 單屬性排序
const byAge = _.orderBy(users, ["age"], ["asc"]);   // 升冪
const byName = _.orderBy(users, ["name"], ["desc"]); // 降冪

console.log(byAge);
```

多屬性排序.ts
```ts
const multiSort = _.orderBy(users, ["age", "name"], ["asc", "desc"]);
```

2. Remeda（型別安全版 Lodash）

安裝remeda
```
npm install remeda
```

```ts
import { sortBy } from "remeda";

const byAge = sortBy(users, (u) => u.age);
const byName = sortBy(users, (u) => u.name);
```

3.內建(ES2023 新增，原生) toSorted

但是寫法還是有點不好閱讀，多排的時候
```
const byAge = users.toSorted((a, b) => a.age - b.age);
```
---
title: '[Day07] Typescript什麼時候用interface'
date: 2025-08-17 21:17:12
categories:
  - [Topic,2025 Typescript]
  - [Code,Javascript]
  - [Code,Typescript]
---
這回想藉此來探討實際應用時何時該用interface?
在過往實務應用時會遇到若每個處理的物件都建interface那一個頁面就動輒1~5個以上。
而算上巢狀物件，就更多了，所以這回利用AI建議來探討一下。

### Q1:詢問我在寫typescript專案，感覺一個頁面每個物件都建立interface太多了，到底哪些才要哪些用any?
#### 解答:
這個問題超多人在 TypeScript 專案會遇到 👍
你現在的困擾其實就是 **型別要不要「嚴格」定義** vs **偷懶用 `any`** 的取捨。

我幫你拆成幾個層次來看：

---

### 🚫 不建議直接用 `any` 的地方

1. **API 回傳的資料**

   * API 是最常變動的地方，如果用 `any`，資料格式一變你就會踩雷。
   * 建立 interface/type 可以立刻知道少了欄位或欄位型態錯誤。
     ✅ 這裡建議要有 interface。

2. **跨檔案、多人協作的資料結構**

   * 例如 User、Product、Order 這種「全專案通用」的物件。
   * 沒型別大家就會各自定義，結果最後爆掉。
     ✅ 要建立 interface 或 type，通常會集中放在 `types/` 資料夾。

3. **複雜邏輯需要推導型別**

   * 假設一個 function 處理一堆欄位，如果沒型別你會不知道 function 需要什麼資料。
     ✅ 建議用 interface 來限制 input/output。

---

### 🟡 可以「偷懶」不用 interface 的地方

1. **一次性的小物件**

   ```ts
   const options = { theme: 'dark', showSidebar: true }
   ```

   * 這種本地小物件，TS 自己會推斷型別，不一定要手動寫 interface。

2. **快速原型 / Demo**

   * 你只是在測試功能，或這頁很快會被刪掉，不需要寫到超嚴格。
   * 可以用 `any` 或 `unknown`，之後再慢慢補齊。

3. **第三方套件輸出的資料很雜**

   * 有些套件沒提供型別，資料又一堆巢狀，硬定義超麻煩。
   * 可以先用 `any`，等真的需要用到再逐步補型別。

---

### 🎯 實務上常見做法

* **Domain Models**（使用者、商品、訂單）：一定寫 interface/type。
* **API Response**：通常用 `type ApiResponse<T> = { data: T; error?: string }` 這種 pattern。
* **小型狀態 / component props**：TS 自己會推斷就不用寫。
* **還不確定結構的資料**：先用 `any` 或 `unknown`，等穩定後再補 interface。

---

👉 簡單原則就是：

* **跨頁面/多人共用 → 要 interface**
* **一次性/很簡單 → 交給 TS 推斷就好**
* **實在太麻煩 → 先用 any/unknown，之後再補**


### Q2.那像是angular 或vue3或react這種 在html上顯示的資料該訂嗎?

這個其實就是 **前端框架 (Angular / Vue3 / React)** 在 **模板 (HTML/JSX)** 顯示資料時，要不要訂型別？

---

## 📌 分情境來看

### 1. **API 資料要渲染到畫面上**

```ts
// 假設從 API 拿到 user
interface User {
  id: number
  name: string
  email?: string
}

users: User[] = [] // Angular
const users = ref<User[]>([]) // Vue3
const [users, setUsers] = useState<User[]>([]) // React
```

* **要型別** ✅
  因為這是 **外部資料來源 (API)**，一旦 API 改了，畫面上就可能噴錯。
  有 interface → TS 在你 `{{ user.name }}` 或 `user.name` 時會自動提示、也能避免 typo。

---

### 2. **本地暫存的小資料 / UI 狀態**

```ts
isLoading = true
selectedTab = 'home'
```

* **不用特別寫 interface** ❌
  這種 TS 會自動推斷，寫 interface 反而太重。
  (除非是 **大型複雜狀態**，才會包一個 `interface UiState`)

---

### 3. **表單資料**

```ts
interface CampForm {
  date: string
  people: number
  note?: string
}

campForm = this.fb.group<CampForm>({
  date: [''],
  people: [0],
  note: [''],
})
```

* **要型別** ✅
  因為表單資料要送 API / 驗證，沒有型別很容易傳錯。

---

### 4. **Component Props**

```ts
// Vue3
defineProps<{ title: string; count?: number }>()

// React
type ButtonProps = {
  label: string
  onClick?: () => void
}
```

* **要型別** ✅
  因為 props 是 **元件的 API**，型別就是保護你和使用者不傳錯。

---

## 🎯 實務上的建議

* **API / Props / 表單** → **一定要 interface/type**
* **畫面上的小資料 (狀態、flag)** → **交給 TS 自動推斷就好**
* **如果型別很麻煩但資料要顯示** → 先 `any` → 之後慢慢抽 interface

---

👉 一句話原則：
**「會跨元件或送 API 的資料 → 訂型別」
「只在本地 UI 用的小資料 → TS 自己推斷就好」**

### Q3.我讓AI幫我判斷了我這還沒整理的Angular程式碼，哪些該省哪些該加。

.ts程式碼
https://github.com/yuhsiang237/CampDay/blob/80af24eb86eb064a22ac7acf649b3b104600f46f/src/app/result/result.ts


我幫你把這份 **Angular v20 程式碼**掃描過一遍，從「哪些要 interface」「哪些可以偷懶」的角度整理 👍

---

## ✅ 建議一定要有 **interface/type**

這些是「跨頁面 or API 資料」，型別能大幅降低錯誤：

* **CampSite**

  * 你已經寫好，對應 CSV → 當然要保留。
* **CampSearch**

  * 來自表單、要跟 API 溝通 → 一定要型別。
* **CampDistData**

  * 你已經用來做 groupBy 的結果，畫面會顯示 district / data → 保留。
* **WeatherAPI**

  * 你有 `cityApiMap: WeatherAPI = weatherAPI as WeatherAPI;` → 建議保留。

👉 這些 interface 應該集中放在 `interfaces/` 資料夾，做「共用 domain 型別」。

---

## 🟡 建議可以再補充型別（目前用 `any` 的地方）

現在很多 `any`，會讓你失去 TS 提示：

* `formData: any;`

  * 建議 → 用 `Partial<CampSearch>` 或 `Record<string, string>`
* `weather: any;`

  * 建議 → 抽成 `interface WeatherResponse`，至少定義 `records.Locations[0].Location` 結構，不然 HTML 綁定會沒提示。
* `locationWeather: any[] = [];`

  * 建議 → 改成 `WeatherLocation[]`（要不要定義完整，看你需求，可以先定義部分欄位）。
* `normalizeWeatherSlots(raw: any): any`

  * 建議 → 回傳型別 `Record<string, (WeatherSlot|null)[]>`，
    至少定義 `WeatherSlot { timeRange: string; label: string; maxTemp?: number; minTemp?: number; weather?: string }`。
* `getFirstDaySlots(districtName: string): any[]`

  * 建議 → 改成 `WeatherSlot[]`。
* `getWeatherByDistrictGrouped(districtName: string): { [date: string]: any[] }`

  * 建議 → `{ [date: string]: WeatherSlot[] }`。
* `getWeatherByLocationAny(location: string)`

  * 這個你用 `any` 還 ok，因為是「整包丟給 chart/table」的 raw data，未來若只取部分再訂也行。

---

## ❌ 可以省略 interface（讓 TS 自動推斷就好）

* `isLoading: boolean = true;`
* `campSites: CampSite[] = [];`（這個其實 TS 會推斷出來，寫 `: CampSite[]` 算是多餘的，但保留也沒問題）
* `campSiteSearchResults: CampSite[] = [];` 同上
* `campDistData: CampDistData[] = [];` 同上

這些小狀態，TS 自己會推斷，寫不寫型別都行。

---

## 🎯 總結 (給你一個規則表)

| 類型                                     | 要不要 interface/type |
| -------------------------------------- | ------------------ |
| **API Response**                       | 要，至少定義會顯示在畫面的欄位    |
| **CSV 轉換後的資料 (CampSite)**              | 要                  |
| **表單資料 (CampSearch)**                  | 要                  |
| **跨頁共用的資料 (CampDistData, WeatherAPI)** | 要                  |
| **畫面 UI 狀態 (isLoading, flag, 小字串)**    | 不用，交給 TS 推斷        |
| **一次性 function local 物件**              | 不用                 |
| **暫時不清楚的資料**                           | 可以先 `any`，後面再抽型別   |

---

👉 如果你要「快速先跑」，我會建議：

* `weather`、`locationWeather` → 先留 `any`
* 其他 API / 表單 / CampSite → 全部要 interface
* 專案穩定後再慢慢把 `weather` 抽型別出來

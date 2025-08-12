---
title: '[Day02] Typescript實作強型別todolist'
date: 2025-08-12 21:17:12
categories:
  - [Topic,2025 Typescript]
  - [Code,Javascript]
  - [Code,Typescript]
---

這回主要使用Typescript時做一個todolist，以強型別方式規範資料，並操作CRUD
以過往實作經驗搭配AI撰寫，在職場正式專案上也很通用的做法

#### 功能
1. 新增(create)
2. 查詢(readAll, readById)
3. 更新(update)
4. 刪除(delete)
5. enum 狀態對應文字取得(getStatusName)

#### 優點

1.可讀性高:
透過 StatusCode.Done、StatusCode.Pending 等命名，
代碼一目了然，比單純用 1、2、3 更好理解。
反向查詢文字 StatusCode[status] 讓狀態展示更友善。
enum 支援數字 ↔ 字串互轉，
程式內可用數字節省空間，UI 顯示可用文字增強可讀性。

2.維護方便:
CRUD 邏輯封裝在 TodoManager 類別中，讓資料操作統一管理，易於擴展與維護。

3.封裝良好:
todos 陣列設為私有 (private)，外部只能透過方法操作資料，

4.型別安全:
status 欄位被定義成 StatusCode enum，
只能接受預先定義的數值（1 | 2 | 3），避免亂輸入或寫錯字串。
編譯器會檢查不合規的狀態賦值，降低執行錯誤風險。

```typescript
// 狀態 enum
enum StatusCode {
  Done = 1,
  Pending = 2,
  InProgress = 3
}

// Todo 型別
interface Todo {
  id: number;
  nametitle: string;
  status: StatusCode;
}

class TodoManager {
  private todos: Todo[] = [];

  // 新增
  create(todo: Todo): void {
    this.todos.push(todo);
  }

  // 讀取全部
  readAll(): Todo[] {
    return [...this.todos]; // 複製避免外部修改
  }

  // 讀取單一
  readById(id: number): Todo | undefined {
    return this.todos.find(t => t.id === id);
  }

  // 更新
  update(id: number, updatedFields: Partial<Todo>): boolean {
    const index = this.todos.findIndex(t => t.id === id);
    if (index === -1) return false;
    this.todos[index] = { ...this.todos[index], ...updatedFields };
    return true;
  }

  // 刪除
  delete(id: number): boolean {
    const originalLength = this.todos.length;
    this.todos = this.todos.filter(t => t.id !== id);
    return this.todos.length < originalLength;
  }

  // 顯示狀態文字版（擴充）
  getStatusName(status: StatusCode): string {
    return StatusCode[status];
  }
}

// 測試 CRUD
const manager = new TodoManager();

// Create
manager.create({ id: 1, nametitle: 'Test Task 1', status: StatusCode.Pending });
manager.create({ id: 2, nametitle: 'Test Task 2', status: StatusCode.InProgress });

// Read All
console.log('All Todos:', manager.readAll());

// Read By Id
console.log('Todo with id=1:', manager.readById(1));

// Update
manager.update(1, { status: StatusCode.Done, nametitle: 'Updated Task 1' });
console.log('After Update:', manager.readById(1));

// Delete
manager.delete(2);
console.log('After Delete:', manager.readAll());

// 取得狀態名稱示範
const todo = manager.readById(1);
if (todo) {
  console.log('Status code:', todo.status);
  console.log('Status name:', manager.getStatusName(todo.status));
}

```
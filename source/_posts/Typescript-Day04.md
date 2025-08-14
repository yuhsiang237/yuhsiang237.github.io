---
title: '[Day04] Typescript 呼叫API整理'
date: 2025-08-14 21:17:12
categories:
  - [Topic,2025 Typescript]
  - [Code,Javascript]
  - [Code,Typescript]
---
這回來是API呼叫方式
常見的API在大型專案中都是會先封裝好，再透過API網址去呼叫。

主要使用axios，因為可以控制版本，相對於原生的fetch而言。

該份API呼叫範例程式碼優點：
1.集中管理 API 主機 URL
2.呼叫端程式碼更乾淨
3.易於維護和擴展
4.支援多環境
5.安全性
6.未來擴充方便

「配置集中化 + 呼叫簡潔化 + 易維護 + 安全 + 支援多環境」

```sh
# API呼叫
npm install axios
npm install --save-dev @types/axios
# API環境檔案
npm install dotenv
```

首先環境檔案

.env
```env
API_BASE_URL=https://jsonplaceholder.typicode.com
API_USERS_PATH=/users
API_POSTS_PATH=/posts
API_COMMENTS_PATH=/comments
```

HttpClient.ts
把 GET 和 POST 都包成 HttpClient 的方法，並保留 TypeScript 泛型支援

```ts
// HttpClient.ts
import axios, { AxiosInstance, AxiosRequestConfig, AxiosResponse } from 'axios';
import dotenv from 'dotenv';

dotenv.config(); // 讀取 .env

class HttpClient {
  private client: AxiosInstance;

  constructor() {
    const baseURL = process.env.API_BASE_URL || '';
    this.client = axios.create({
      baseURL,
      timeout: 5000,
      headers: { 'Content-Type': 'application/json' },
    });
  }

  public async getData<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
    const response: AxiosResponse<T> = await this.client.get<T>(url, config);
    return response.data;
  }

  public async postData<T>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T> {
    const response: AxiosResponse<T> = await this.client.post<T>(url, data, config);
    return response.data;
  }
}

export default HttpClient;
```

型別定義

type.ts
```ts
// types.ts
export interface User {
  id: number;
  name: string;
  email: string;
}

export interface CreateUserRequest {
  name: string;
  email: string;
}
```

呼叫範例:
main.ts

```ts
// main.ts
import dotenv from 'dotenv';
dotenv.config(); // 讀取 .env

import HttpClient from './HttpClient';
import { User, CreateUserRequest } from './types';

const api = new HttpClient(); // 不用傳 baseURL，已經在 HttpClient 裡讀取

async function run() {
  // GET 所有使用者
  const users: User[] = await api.getData<User[]>(process.env.API_USERS_PATH || '/users');
  console.log('Users:', users);

  // GET 單一使用者
  const user: User = await api.getData<User>(`${process.env.API_USERS_PATH}/1`);
  console.log('User 1:', user);

  // POST 新使用者
  const newUserData: CreateUserRequest = { name: 'John Doe', email: 'john@example.com' };
  const newUser: User = await api.postData<User>(process.env.API_USERS_PATH || '/users', newUserData);
  console.log('New User:', newUser);
}

run();
```

總結：
HttpClient 自動抓 .env 的 API_BASE_URL，呼叫時不用再傳。
.env 仍然可以管理不同環境的 URL 與 API 路徑。
main.ts 只要 import HttpClient + 用 .env 定義的路徑即可。


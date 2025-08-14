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

```sh
npm install axios
npm install --save-dev @types/axios
```

HttpClient.ts
把 GET 和 POST 都包成 HttpClient 的方法，並保留 TypeScript 泛型支援

```ts
// HttpClient.ts
import axios, { AxiosInstance, AxiosRequestConfig } from 'axios';

class HttpClient {
  private client: AxiosInstance;

  constructor(baseURL: string) {
    this.client = axios.create({
      baseURL,
      timeout: 5000,
      headers: {
        'Content-Type': 'application/json',
      },
    });
  }

  public async get<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
    const response = await this.client.get<T>(url, config);
    return response.data;
  }

  public async post<T>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T> {
    const response = await this.client.post<T>(url, data, config);
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
import HttpClient from './HttpClient';
import { User, CreateUserRequest } from './types';

// 建立實例（設定 API 主機）
const api = new HttpClient('https://jsonplaceholder.typicode.com');

async function run() {
  try {
    // GET 範例
    const user = await api.get<User>('/users/1');
    console.log('GET User:', user);

    // POST 範例
    const newUserData: CreateUserRequest = {
      name: 'John Doe',
      email: 'john@example.com',
    };
    const newUser = await api.post<User>('/users', newUserData);
    console.log('POST New User:', newUser);
  } catch (error) {
    console.error('API Error:', error);
  }
}

run();
```
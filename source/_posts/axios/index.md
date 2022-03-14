---
title: Axios拦截请求封装
author: 0hayo
avatar: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/custom/avatar.jpg
authorLink: 
authorAbout: nothing
authorDesc: nothing
categories: 
    - Axios
date: 2022-02-22 14:57:17
comments: true
tags: 
    - Axios
    - Axios拦截
keywords: 0hayo
description: 站在自己所热爱的世界里！
photos: https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/loader/orange.progress-bar-stripe-loader.svg
---

## Axios拦截请求封装

请求封装

```js
import { ApiError } from '@/structure/Request';
import { Config } from '@/utils/Config';
import axios, { AxiosRequestConfig } from 'axios';

const service = axios.create({
  baseURL: Config.API_ADDRESS,
  timeout: Config.TIME_OUT
});

// 请求拦截器
service.interceptors.request.use(
  (config: AxiosRequestConfig) => {
    config.headers['token'] = Config.getToken() || '';
    return config;
  },
  error => {
    Promise.reject(error);
  }
);

// 响应拦截器
service.interceptors.response.use(
  response => {
    const data = response.data;
    if (data.code !== 200) {
      switch (data.code) {
        case 400:
          return Promise.reject(ApiError.BadRequest);
        case 401:
          return Promise.reject(ApiError.Unauthorized);
        case 403:
          return Promise.reject(ApiError.Forbidden);
        case 404:
          return Promise.reject(ApiError.ResourceNotFound);
        case 500:
          return Promise.reject(ApiError.ServerSideError);
        case 1001:
          return Promise.reject(ApiError.CustomError.from(data.msg));
        default:
          return Promise.reject(ApiError.Unexpected.from(data.msg));
      }
    } else {
      return data;
    }
  },
  error => {
    return Promise.reject(error);
  }
);

export default service;

```

调用封装

```js
import { ApiResponse } from '@/structure/Request';
import service from '@/utils/service';

export class CommonApi {
  public static async getTimestamp(): Promise<number> {
    const response = await service.get<void, ApiResponse<number>>('/utils/getSystemTime');
    return response.result;
  }
}
```

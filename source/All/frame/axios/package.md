---
title: 封装Axios
date: 2021-09-30 14:31:02
toc: true
---

## 封装
```js
//axios.js
import axios from 'axios';
import { Message } from 'view-design';

let process_env_API_BASE_URL = 'http://www.xxx.com';

//配置Axios
function createAxiosInstance() {
  //1、定义一些常用的配置
  const instance = axios.create({
    baseURL: process_env_API_BASE_URL,
    timeout: 5000,
    headers: {
      //定义统一的请求头部
      post: {
        'Content-Type': 'application/json'
      }
    }
  });

  //2、请求之前添加一些我们需要的操作
  //比如添加token,请求参数判空
  instance.interceptors.request.use((config) => {
    //可添加开启loading效果的函数 loading.open()
    //token存在就添加到请求头中
    // token && (config.headers.Authorization = token)

    //也可以添加过滤-》用于过滤请求参数中的null undefined '' 的函数
    //cleanObject();

    return config;
  });

  //3、请求成功返回的数据
  //比如处理成功返回的数据(后端返回的data数据可能嵌套了很多层，你可以直接返回你需要的data数据，这样业务代码就可以直接拿到最终的数据，不用每次去解构之类的)
  instance.interceptors.response.use(
    (response) => {
      //可添加关闭loading的函数loading.close()
      //解构出返回结果的数据
      const res = response.data;
      //对自定义code码进行判断，将成功的数据返回储区
      const validateStatus = /^(2|3)\d{2}$/;//code以2或3开头的视为请求成功
      if (validateStatus.test(res.code)) {
        return res;//直接return我们需要的data
      }
      //判断失败的code码并做出提示操作
      if (res.code === '401') {
        Message.error(res.msg);
      } else {
        Message.warning(res.msg);
      }
      return Promise.reject(res);
    },
    (error) => {
      //loading.close()
      /*
      可以用于判断token是否失效
      if (error.response.status === 401) {
        Message.error('token失效，请重新登录');
        removeStorageToken();
        setTimeout(() => {
          window.location.href = '/login';
        }, 2000);
      }
      */
      //判断网络问题
      if (!window.navigator.onLine) {
        Message.warning('网络异常，请检查网络是否正常连接');
      } else if (error.code === 'ECONNABORTED') {
        Message.warning('请求超时');
      } else {
        Message.warning('服务器异常，请联系管理员')
      }
      return Promise.reject(error);//将错误继续返回给具体页面
    },
  );

  return instance;
}

export const request = createAxiosInstance();
```

## 使用
```js
//xxx.js
import { request } from 'axios.js';

export function getData(params) {
  return request.get(`/xxx/yyy?${params}`);
}
```

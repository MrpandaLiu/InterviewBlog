---
title: 用两种方法实现Ajax
date: 2020-07-16 22:20:13
tags: [JavaScript,Ajax]
category: [前端]
---

Ajax，简而言之就是**异步的JavaScript与XML技术**，它让我们可以无需刷新页面就能通过http请求获取到数据（不亏是异步呢！），在项目中想必很多人已经使用过了jQuery的Ajax和前端框架常用axios，那么这次就和熊猫一起来动手实现一个Ajax吧

先看看Ajax的整体结构，也就是常说的4个步骤
``` javascript
  // 1
  const xhr = new  XMLHttpRequest();
  // 2
  xhr.open(method, url, true);
  // 3
  xhr.send()
  // 4
  xhr.onreadystatechange = () => {
    if(xhr.readyState === 4) {
    }
  }
```

我们要做的就是让它变得可配置，增加配置项。那么先实现JQ版本：
``` javascript
Ajax({
  url: "http://localhost:3000",
  method: "post",
  data: {
    username: "panda",
    password: "123"
  },
  success: (res) => {
    console.log(res.msg);
  }
})
```
要想让上述代码可执行，我们封装一个高阶函数即可！
``` javascript
const Ajax = ({
  // 配置项默认参数
  url = "",
  method = "get",
  params = {},
  headers = {},
  success = () => {},
  error = () => {}
}) => {
  let xhr;
  // 创建对象
  if(window.XMLHttpRequest) {
    xhr = new XMLHttpRequest();
  } else {
    // 兼容
    xhr = new ActiveXObject("Microsoft.XMLHTTP");
  }
  // 设置get参数
  if(method === "get" && JSON.stringify(params) !== "{}") {
    url += "?";
    for(let i in params) {
      url += `${i}=${params[i]}&`;
    }
  }
  // 规定请求
  xhr.open(method, url, true);
  // 设置请求头
  for(let i in headers) {
    xhr.setRequestHeader(i, headers[i]);
  }
  // 发送请求
  if(method === "post") {
    xhr.setRequestHeader("Content-Type", "application/json");
    // 字符串序列化
    xhr.send(JSON.stringify(data));
  } else {
    xhr.send();
  }

  // 响应事件
  xhr.onreadystatechange = () => {
    // 判断是否请求已完成，且响应就绪
    if(xhr.readyState === 4) {
      if((xhr.status >= 200 && xhr.status <= 300) || xhr.status === 0) {
        const result = xhr.responseText;
        success(JSON.parse(result));
      }
    }
  }
  xhr.onerror = (err) => {
    error(err);
  }
}
```

在jQ版本，已经实现`选择get/post方法`、`拼接get的参数params`、`添加请求头`、`添加请求体`、`成功or失败的回调函数`，不过利用这种回调会有一个坏处，那就是**回调地狱**，如果需要一个请求之后拿到请求信息再去发起下一次请求，然后又拿到请求信息发起下一次请求，如果嵌套过多，就会导致{}大括号越来越深，代码整体像一个倒着的V字型，这就有了promise，通过链式调用，让代码更清晰

``` javascript
Ajax({
  url: "http://localhost:3000",
  method: "post",
  data: {
    username: "panda",
    password: "123"
  }  
}).then(res => {
  console.log(res.msg);
})
```
我们就以这个函数为例来试下吧

``` javascript
const Ajax = ({
  // 配置项默认参数
  url = "",
  method = "get",
  params = {},
  headers = {},
  success = () => {},
  error = () => {}
}) => {
  return new Promise((resolve,reject) => {
    let xhr;
    // 创建对象
    if(window.XMLHttpRequest) {
      xhr = new XMLHttpRequest();
    } else {
      // 兼容
      xhr = new ActiveXObject("Microsoft.XMLHTTP");
    }
    // 设置get参数
    if(method === "get" && JSON.stringify(params) !== "{}") {
      url += "?";
      for(let i in params) {
        url += `${i}=${params[i]}&`;
      }
    }
    // 规定请求
    xhr.open(method, url, true);
    // 设置请求头
    for(let i in headers) {
      xhr.setRequestHeader(i, headers[i]);
    }
    // 发送请求
    if(method === "post") {
      xhr.setRequestHeader("Content-Type", "application/json");
      // 字符串序列化
      xhr.send(JSON.stringify(data));
    } else {
      xhr.send();
    }

    // 响应事件
    xhr.onreadystatechange = () => {
      // 判断是否请求已完成，且响应就绪
      if(xhr.readyState === 4) {
        if((xhr.status >= 200 && xhr.status <= 300) || xhr.status === 0) {
          const result = xhr.responseText;
          resolve(JSON.parse(result));
        }
      }
    }
  })
}
```




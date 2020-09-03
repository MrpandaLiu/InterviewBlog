# 一、概述

express是一个基于Node.js平台，快速、开放、极简的Web开发框架

今天我们就来实现express中get、post、use等方法，让下面的代码可执行

完整代码请访问：[simple-express](https://github.com/MrPand111/simple-Express.js)

``` js
const express = require('./myexpress');
const app = express();
const router = app.Router();

app.use((req, res, next) => {
  console.log('ppanda');
  next();
})

app.get('/panda', (req, res) => {
  res.end('this is mrpanda');
})

router.get('/bear', (req, res) => {
  res.end('this is bear');
})

app.post('/cat', (req, res) => {
  res.end('this is cat');
})

app.listen(3000, () => {
  console.log('running...');
});
```


# 二、主体结构

我们将项目创建在myexpress文件夹下

主要思想就是**每一个请求和每一个中间件都是layer，用一个数组来存，在监听后按照顺序来执行or匹配**

``` js
// index.js
const http = require('http');
const url = require('url');
const router = require('./router');
const createLayer = require('./layer');

class App {
  // 初始化
  constructor() {
    ...
  }

  // server app
  app(req, res) {
    ...
  }

  // 挂载 http methods
  useMethods() {
    ...
  }

  // all方法
  all(path, fn) {
    this.router.routes.push(createLayer('all', path, fn));
  }

  // 挂载方法
  use(path, fn) {
    // 根据不同的参数
    if (typeof fn !== 'function') {
      fn = path;
      path = '/';
    }
    this.router.routes.push(createLayer('middleware', path, fn));
  }

  Router() {
    return this.router;
  }

  // 封装监听端口
  listen(...args) {
    // 改变作用域
    const server = http.createServer(this.app.bind(this));
    // 参数传递
    server.listen(...args);
  }
}

// 创建express实例
const createApplication = () => {
  const app = new App();
  return app;
}

module.exports = createApplication;
```

构造函数

``` js
constructor() {
  // 创建路由
  this.router = router();
  // 内置中间件 代理部分属性
  // 用url模块解析req.url
  this.use((req, res, next) => {
    const {
      pathname,
      query
    } = url.parse(req.url, true);
    const hostname = req.headers['host'].split(":")[0];
    req.path = pathname;
    req.query = query;
    req.hostname = hostname;
    next();
  })
  // 挂载 http method
  this.useMethods();
}
```

app实例

``` js
app(req, res) {
  // 获取请求的方法
  const md = req.method.toLowerCase();
  // 获取请求的路径
  const {
    pathname
  } = url.parse(req.url, true);
  let index = 0;
  // next函数
  const next = (err) => {
    // 如果没找到对应的layer则返回
    if (index === this.router.routes.length) return res.end(`Cannot ${md} ${pathname}`);
    // 获取layer信息
    const {
      method,
      path,
      fn
    } = this.router.routes[index++];
    // 如果错误收集中间件
    if (err) {
      // 错误收集中间件有四个参数
      if (fn.length === 4) fn(err, req, res, next);
      else next(err);
    } else {
      // 如果是中间件
      if (method === 'middleware') {
        // 匹配路由
        if (path === '/' || path.indexOf(pathname) === 0) fn(req, res, next);
        else next();
      } else {
        // 如果是请求
        // 匹配方法和路由
        if ((method === md || method === 'all') && (path === pathname || path === '*')) return fn(req, res);
        else next();
      }
    }
  }
  next();
}
```

挂载http methods 

``` js
useMethods() {
  // 遍历http提供的所有方法
  http.METHODS.forEach(method => {
    // 大小写转化
    method = method.toLocaleLowerCase();
    // 为实例添加方法
    this[method] = (path, fn) => {
      this.router.routes.push(createLayer(method, path, fn));
    }
  })
}
```

# 三、其他模块

路由对象

``` js
// router.js
const http = require('http');
const createLayer = require('./layer');

class Router {
  constructor() {
    this.routes = [];
    this.useMethods();
  }

  // 挂载 http methods
  useMethods() {
    http.METHODS.forEach(method => {
      method = method.toLocaleLowerCase();
      this[method] = (path, fn) => {
        this.routes.push(createLayer(method, path, fn));
      }
    })
  }
}

const createRouter = () => {
  return new Router();
}

module.exports = createRouter;
```

layer对象

``` js
// layer.js
const createLayer = (m, p, f) => {
  return {
    method: m,
    path: p,
    fn: f
  };
}

module.exports = createLayer;
```
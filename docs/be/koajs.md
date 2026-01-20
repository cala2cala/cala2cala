# koajs学习笔记

koa是基于 Node.js的web框架，体积小，实现了HTTP服务的功能，洋葱模式的设计来加载中间件。

Koa Context 将 node 的 request 和 response 对象封装到单个对象中，为编写 Web 应用程序和 API 提供了许多有用的方法。

## 洋葱模型的实现

### 中间件用法

```javascript
const Koa = require("./koa");
const app = new Koa();

const a1 = async (ctx, next) => {
  console.log(111, ctx.request.method);
  console.log(">>1");
  await next();
  console.log("<<1");
};
const a2 = async (ctx, next) => {
  console.log(">>2");
  await next();
  console.log("<<2");
};
const a3 = async (ctx, next) => {
  console.log(">>3");
  await next();
  console.log("<<3");
};
app.use(a1);
app.use(a2);
app.use(a3);
// app.use(async (ctx) => {
//   ctx.body = "Hello World";
// });

app.listen(3000);
```

### 洋葱模型的实现

```javascript
const http = require("http");
const context = require("./context");
const request = require("./request");
const response = require("./response");
module.exports = class Application {
  constructor(options) {
    this.middleware = [];
    this.context = Object.create(context);
    this.request = Object.create(request);
    this.response = Object.create(response);
  }
  listen(...args) {
    // debug("listen");
    // const server = http.createServer(this.callback());
    // return server.listen(...args);
    const server = http.createServer(this.callback());
    server.listen(...args);

    // (req, res) => {
    //   res.writeHead(200, { "Content-Type": "application/json" });
    //   res.end(
    //     JSON.stringify({
    //       data: "Hello World!",
    //     })
    //   );
    // }
  }
  createContext(req, res) {
    let ctx = Object.create(this.context);
    ctx.request = Object.create(this.request);
    ctx.response = Object.create(this.response);
    ctx.req = ctx.request.req = req;
    ctx.res = ctx.response.res = res;
    return ctx;
  }

  //异步递归遍历中间件处理函数
  compose(middleware) {
    return function (context) {
      const dispatch = (index) => {
        console.log("index", index);
        if (index >= middleware.length) {
          return Promise.resolve();
        }
        const fn = middleware[index];
        //TODO 上下文对象

        return Promise.resolve(fn(context, () => dispatch(index + 1)));
      };
      //返回第一个中间件处理函数
      return dispatch(0);
    };
  }

  callback() {
    const fnMiddleware = this.compose(this.middleware);
    const handleRequest = (req, res) => {
      const context = this.createContext(req, res);
      fnMiddleware(context)
        .then(() => {
          res.end();
        })
        .catch((err) => {
          console.log("callback err", err);
        });
    };
    return handleRequest;
  }

  use(middleware) {
    this.middleware.push(middleware);
  }
};
```

通过

```javascript
const server = http.createServer(this.callback());
server.listen(...args);
```

创建服务器，http.createServer传入一个函数，函数的入参是http请求request和response。

监听函数

```javascript
const handleRequest = (req, res) => {
  const context = this.createContext(req, res);
  fnMiddleware(context) //dispatch(0)，dispatch函数会递归调用下一个中间件
    .then(() => {
      res.end();
    })
    .catch((err) => {
      console.log("callback err", err);
    });
};
```

fnMiddleware按照洋葱模型执行中间件，即递归调用中间件，并传入每次请求创建的context。fnMiddleware是compose函数的返回值。

```javascript
compose(middleware) {
    return function (context) { // fnMiddleware函数，递归调用
      const dispatch = (index) => {
        if (index >= middleware.length) {
          return Promise.resolve();
        }
        const fn = middleware[index];
        return Promise.resolve(fn(context, () => dispatch(index + 1)));
      };
      //返回第一个中间件处理函数
      return dispatch(0);
    };
  }
```

fn是每一个注册的中间件。

## koa实战项目必备工具

- nodemon 监听文件变化，自动重启服务
- dotenv 根目录加载.env文件内容，写到process.env里
- [koa-router](https://github.com/koajs/router)批量注册路由
- [koa-static](https://github.com/koajs/static)管理静态文件
- [koa-mount](https://github.com/koajs/mount) 设置请求路径
- [koa-compose](https://github.com/koajs/compose) 组合中间件
- [bcryptjs](https://www.npmjs.com/package/bcryptjs) 加密，可以用来保存用户密码
- [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken) 生成用户令牌

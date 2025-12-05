# 实现 mini qiankun

## 什么是 qiankun

qiankun 是微前端架构的一种实现。那什么是微前端架构呢？

微前端是一种多个团队通过独立发布功能的方式来共同构建现代化 web 应用的技术手段及方法策略。

简单来说，一个前端应用由多个业务方向的多个项目构成，就构成了微前端架构。

### 微前端架构具备以下几个核心价值：

- 技术栈无关
  主框架不限制接入应用的技术栈，微应用具备完全自主权

- 独立开发、独立部署
  微应用仓库独立，前后端可独立开发，部署完成后主框架自动完成同步更新

- 增量升级
  在面对各种复杂场景时，我们通常很难对一个已经存在的系统做全量的技术栈升级或重构，而微前端是一种非常好的实施渐进式重构的手段和策略

- 独立运行时
  每个微应用之间状态隔离，运行时状态不共享

微前端架构旨在解决单体应用在一个相对长的时间跨度下，由于参与的人员、团队的增多、变迁，从一个普通应用演变成一个巨石应用(Frontend Monolith)后，随之而来的应用不可维护的问题。这类问题在企业级 Web 应用中尤其常见。

### qiankun 是一个基于 single-spa 的微前端实现库

符合以上微前端架构的核心要求。

我在公司 B 端项目中使用，将巨石应用，根据业务方向拆分成多个子应用。

qiankun 的接入成本较低，主项目安装和注册，子项目导出生命周期钩子和打包配置下即可。

qiankun 接入可以参见[官网](https://qiankun.umijs.org/zh/guide/getting-started#1-%E5%AE%89%E8%A3%85-qiankun)。

## qiankun 的实现原理

主项目安装 qiankun，给子项目一个容器，qiankun 在父项目中处理子项目的注册、加载、卸载。

qiankun 会对路由进行监听，当匹配到子项目注册的 activeRule 时，qiankun 按照 entry 加载和渲染子项目到 container 中。

### 监听路由

前端路由分哈希路由和 history 路由。

哈希路由通过 window.onhashchange 监听。

history 路由通过 window.onpopstate 可以监听 history.go, history.back, history.forward 事件。pushState 和 replaceState 通过对事件重写实现监听。
以下是实现 history 路由监听的代码。

```javascript
export const rewriteRouter = () => {
  // 1.监视路由变化

  // hash 路由 通过window.onhashchange 监听

  // history 路由
  // history.go, history.back, history.forward 使用 popstate事件监听， 事件：window.onpopstate
  window.addEventListener("popstate", () => {
    console.log("router：popstate");
    handleRouter();
  });

  // pushState，replaceState 需要通过函数重写的方式劫持
  const rawPushState = window.history.pushState; // 存储 pushState 原函数
  window.history.pushState = (...args) => {
    // args 是[state, title, url]
    rawPushState.apply(window.history, args); // 普通函数的 this 是调用时决定的，因此这里需要更改this为window.history
    console.log("router：监视到 pushState 变化了");
    handleRouter();
  };

  const rawReplaceState = window.history.replaceState; // 存储 pushState 原函数
  window.history.replaceState = (...args) => {
    rawReplaceState.apply(window.history, args);
    console.log("router：监视到 replaceState 变化了");
    handleRouter();
  };
};
```

### html entry 加载（和卸载）子应用

通过 activeRule 匹配到当前子应用后，通过 fetch 方法加载子应用。

```javascript
export function fetchResource(url) {
  return fetch(url).then((res) => res.text());
}
```

获取到子应用的 HTML 文件后，通过 innerHTML 直接插入页面并不会有内容。因为子应用的内容需要通过 JS 来渲染。浏览器出于安全考虑，不会直接执行 innerHTML 里的 script。因此需要手动执行
脚本。

首先通过 getExternalScripts 方法获取 script 的内容，然后通过 execScripts 执行 JS。这里通过 eval 方法来执行子项目的 JS 代码。

```javascript
export const importHTML = async (app) => {
  const url = app.entry;

  // 请求获取子应用 返回内容
  const html = await fetchResource(url);
  const template = document.createElement("div");
  template.innerHTML = html; // 将返回内容挂载在自定义的节点下，方便对齐进行操作
  const scripts = [...template.querySelectorAll("script")];

  // 获取所有script标签代码： [代码， 代码]
  function getExternalScripts() {
    // 1.客户端渲染需要通过执行js生成内容
    // 2.innerhtml 中的script 不会加载执行
    return Promise.all(
      scripts.map((script) => {
        const src = script.getAttribute("src");
        if (!src) {
          // 没有src外链，说明是content型 脚本 ，获取脚本下内容
          return Promise.resolve(script.innerHTML);
        }
        // 如果有src外链，则请求然后获取脚本下内容

        return fetchResource(src.startsWith("http") ? src : `${url}${src}`);
      })
    );
  }

  // 获取并执行所有的script代码
  async function execScripts() {
    const scripts = await getExternalScripts();
    const module = { exports: {} };
    const exports = module.exports;
    // 因为umd 格式会判断当前环境有没有 module 和 exports， 所以我们可以直接在当前环境构造出来，这样子就会将工厂函数返回结果赋值给module.exports
    scripts.forEach((code) => {
      // eval 执行的代码可以访问外部作用域
      // eval(code);
      ((window, module, exports) => {
        try {
          eval(code);
        } catch (error) {
          console.log(error);
        }
      })(app.sandbox.box, module, exports);
    }); // 遍历执行代码
    return module.exports;
  }

  return {
    template,
    getExternalScripts,
    execScripts,
  };
};
```

需要注意的是，在执行子项目 script 后，需要拿到子项目在入口文件导出的生命周期钩子。qiankun 的子项目在打包时需要通过 umd 的格式打包。umd 的格式打包参考如下：

```javascript
(function webpackUniversalModuleDefinition(root, factory) {
	if(typeof exports === 'object' && typeof module === 'object')
		module.exports = factory();
	else if(typeof define === 'function' && define.amd)
		define([], factory);
	else if(typeof exports === 'object')
		exports["vue2"] = factory();
	else
		root["vue2"] = factory();
})(self, function() {...})
```

因此可以通过构造 module 和 exports 获取入口文件导出的生命周期钩子函数。qiankun 会在合适的时机调用。

整个执行过程参考下面代码：

```javascript
export const handleRouter = () => {
  // 如果子应用实例存在，则卸载
  if (app) {
    app.sandbox.inactive(); // 关闭沙箱
    unmount();
  }

  // 创建一个异步任务将更新逻辑放到最后执行防止拿不到目标节点
  setTimeout(async () => {
    console.log("路由变化");

    // 2.匹配子应用
    // 2.1 获取当前路由路由 window.location.pathname

    // 2.2 去apps里面查找
    const apps = getApps(); // 获取app列表
    app = apps.find((item) =>
      window.location.pathname.startsWith(item.activeRule)
    ); // 获取目标app
    if (!app) {
      // 如果没有匹配到app 则直接返回
      return;
    }

    // 创建沙箱实例
    if (!app.sandbox) {
      app.sandbox = new sandbox();
    }

    // 3.加载子应用
    container = document.querySelector(app.container); // 获得入口
    container.innerHTML = "";
    const { template, execScripts } = await importHTML(app);
    container.appendChild(template); // 插入目标节点

    // 设置全局乾坤变量
    window.__POWERED_BY_QIANKUN__ = true; // 告知子应用在基座下渲染

    lifeCycle = await execScripts();

    // 将 生命周期 挂载到 子应用列表中
    app.mount = lifeCycle.mount;
    app.unmount = lifeCycle.unmount;
    app.bootstrap = lifeCycle.bootstrap;

    app.sandbox.active(); // 启动沙箱
    // 执行生命周期函数
    bootstrap();
    mount();
  }, 0);
};
```

## JS 沙箱

子项目的 JS 代码需要运行在 JS 沙箱中。

沙箱是一个隔离的执行环境，用于安全地运行子项目代码，防止多个子项目之间互相影响。
在子项目运行前，开启沙箱。在子项目运行后，关闭该子项目的沙箱。

每个子项目都可能会对全局变量 window 进行更改，因此子项目不能直接更改 window。那如何将子项目和 window 进行隔离呢？我们首先定义一个和 window 关联的 app.sandbox.box。在 JavaScript 中，eval() 函数执行时会继承它所在作用域的词法环境。因此在使用 eval 即系代码时，将 app.sandbox.box 作为 window 参数传入，这样子项目代码对 window 的修改将变为对 app.sandbox.box 的修改。

```javascript
((window, module, exports) => {
  try {
    eval(code);
  } catch (error) {
    console.log(error);
  }
})(app.sandbox.box, module, exports);
```

app.sandbox.box 是什么呢？是对一个空对象的代理。当进行 set 操作时，set 在空对象上。当进行 get 操作时，优先访问代理对象，如果代理对象没有，再从真正的 window 对象上访问。
从而实现了拦截子应用对 window 对象的直接更改。

```javascript
export class sandbox {
  // 沙箱完整版
  running = false; // 是否启动沙箱

  fakeWindow = {};
  realWindow = window;

  box = null; // 沙箱proxy实例

  constructor() {
    this.box = new Proxy(this.fakeWindow, {
      get: (target, key) => {
        if (Reflect.has(target, key)) return Reflect.get(target, key); // 优先代理对象

        // 否则从window上找
        const result = Reflect.get(this.realWindow, key);
        if (typeof result == "function") {
          // 如果当前项在window中为函数则返回待执行函数
          return result.bind(this.realWindow);
        }
        return result;
      },
      set: (target, key, value) => {
        // 沙箱启动的前提下
        if (this.running) {
          Reflect.set(target, key, value);
        }
        return true;
      },
    });
  }
  active() {
    this.running = true;
  }
  inactive() {
    this.running = false;
    this.fakeWindow = {};
  }
}
```

## 项目 demo

这里访问[demo](https://github.com/cala2cala/miniQiankun)可以下载和运行以上代码。

# Webpack5 Module Federation 初探

## 想象这样一个场景

项目 A 中有一个组件，项目 B 需要使用。项目 A 和 B 是单独开发和部署的独立项目，如何实现组件复用呢？

可以把复用组件抽离到单独 NPM 实现复用，但是这样维护成本会变高。还有别的方式吗？Webpack5 为我们提供了一种新的方式，A 项目作为生产者，在 webpack 配置文件中目标组件暴露出去，B 项目作为消费者，在 webpack 配置文件中导入远程组件，即可在项目内使用目标组件。

## 举例说明

在 app1 项目中导出 Header.vue 组件，与普通的 vue 组件写法一样。webpack plugins 中增加如下配置：

```javascript
new ModuleFederationPlugin({
    // 提供给其他项目加载的文件
    filename: "remoteEntry.js",
    // 唯一ID，用于标记当前项目
    name: "app1",
    // 需要暴露的模块，使用时通过 `${name}/${expose}` 引入
    exposes: {
    './Header': "./src/components/Header.vue",
    },
}),
```

在 app2 项目中使用远程 Header.vue 组件，动态引入。

```javascript
<template>
  <div id="app">
    <Header name="app2，使用 app1 的 header"/>
  </div>
</template>
<script>
export default {
  components: {
    Header: () => import('app1/Header')
  },
}
</script>
```

app2 项目 webpack plugins 中增加如下配置：

```javascript
new ModuleFederationPlugin({
  // 配置导入的模块映射
  // [导入项目name]："[导入项目name]@ [访问路径]/[导入模块filename]"
  remotes: {
    app1: "app1@http://localhost:3001/remoteEntry.js",
  },
});
```

代码 [demo](https://github.com/cala2cala/module-federation-demo)。

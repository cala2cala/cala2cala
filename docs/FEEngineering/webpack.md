# webpack
webpack 是一个用于现代 JavaScript 应用程序的 静态模块打包工具。

当 webpack 处理应用程序时，它会在内部从一个或多个入口点构建一个 依赖图(dependency graph)，然后将你项目中所需的每一个模块组合成一个或多个 bundles，它们均为静态资源，用于展示你的内容。

## loader

webpack 只能理解 JavaScript 和 JSON 文件，这是 webpack 开箱可用的自带能力。

loader 让 webpack 能够去处理其他类型的文件，并将它们转换为有效 模块，以供应用程序使用，以及被添加到依赖图中。

Loader 的执行顺序是从右到左（从下到上）

### css 相关的loader

### css-loader
css-loader 的核心工作是解释（interpret）@import 和 url() 语句，并将 CSS 文件转换为 Webpack 能够理解的 JavaScript 模块。

### style-loader
将 CSS 注入到 DOM 中 - 它接收 CSS 代码（通常来自 css-loader 的处理结果），然后创建 <style> 标签并将 CSS 插入到 HTML 文档的 <head> 中。

### vue-style-loader
style-loader 的 "Vue 定制增强版"。
继承了 style-loader 的所有核心功能，并在此基础上为 Vue 的单文件组件（.vue 文件）添加了服务器端渲染（SSR）和热重载（HMR）等关键特性的支持。

## 插件(plugin)

插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量。

### MiniCssExtractPlugin
对于生产环境，我们通常不会用 style-loader（它会把样式注入到 JS 里），而是使用 mini-css-extract-plugin 对应的 loader。

## webpack经典配置


tree-shaking 

拆包

## Webpack Dev Server 做了那些事情
1. 启动一个本地开发服务器( Express 框架构)
2. 编译和监听源代码:将编译结果保存在内存中
3. 提供资源服务
4. 实现热模块替换 (HMR - Hot Module Replacement)
5. 提供代理功能 (Proxy)：解决跨域问题
6. 提供友好的开发体验功能

### 热更新
1. 监听文件：Webpack Dev Server（实际上是底层的 webpack 编译器）会监视你的源代码文件系统中的所有文件。当它检测到一个文件被修改并保存时：

2. 增量编译：它不会重新构建整个项目，而是只重新编译更改过的模块及其依赖。这比完整构建要快得多。

3. 推送更新：编译完成后，Webpack Dev Server 通过 WebSocket 连接向浏览器主动推送一条消息，通知浏览器有模块已经更新。

4. 应用更新：浏览器接收到通知后，HMR 运行时会尝试替换、添加或删除正在运行的应用程序中的模块，而无需完全刷新整个页面。这保留了应用程序的当前状态（例如，你在输入框里输入的文字、组件的状态等）。


## chainWebpack用法

webpack-chain 的语法

## 参考文档
1. https://webpack.docschina.org/configuration/dev-server/#root
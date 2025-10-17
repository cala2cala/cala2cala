# webpack

webpack 是一个用于现代 JavaScript 应用程序的 静态模块打包工具。

当 webpack 处理应用程序时，它会在内部从一个或多个入口点构建一个 依赖图(dependency graph)，然后将你项目中所需的每一个模块组合成一个或多个 bundles，它们均为静态资源，用于展示你的内容。

## loader

webpack 只能理解 JavaScript 和 JSON 文件，这是 webpack 开箱可用的自带能力。

loader 让 webpack 能够去处理其他类型的文件，并将它们转换为有效 模块，以供应用程序使用，以及被添加到依赖图中。

loader 的执行顺序是从右到左（从下到上）。

loader是一个函数，被webpack实例调用，对源代码进行处理。

### css 相关的loader

#### css-loader

css-loader 的核心工作是解释（interpret）@import 和 url() 语句，并将 CSS 文件转换为 Webpack 能够理解的 JavaScript 模块。

#### style-loader

将 CSS 注入到 DOM 中 - 它接收 CSS 代码（通常来自 css-loader 的处理结果），然后创建 <style\> 标签并将 CSS 插入到 HTML 文档的 <head\> 中。

#### vue-style-loader

style-loader 的 "Vue 定制增强版"。
继承了 style-loader 的所有核心功能，并在此基础上为 Vue 的单文件组件（.vue 文件）添加了服务器端渲染（SSR）和热重载（HMR）等关键特性的支持。

### ts-loader

主要工作是在 webpack 构建过程中，将 TypeScript 文件（.ts 或 .tsx）转换成 JavaScript 文件（.js），以便浏览器或 JavaScript 环境能够执行。

### vue-loader

它允许你以单文件组件（SFC）的形式编写 Vue 组件。

- 单文件组件支持：允许将模板、脚本和样式放在一个 .vue 文件中

- 预处理器支持：可以使用各种预处理器如 Pug、SCSS 和 TypeScript

- 作用域 CSS：通过 scoped 属性实现组件样式封装

- 热重载：在开发过程中保持应用状态的同时更新组件

- 代码分割：支持异步组件和优化打包

### babel-loader

- 语法转换：将 ES6+ 语法转换为向后兼容的 JavaScript 版本

- Polyfill 支持：通过 @babel/polyfill 提供缺失的内置功能

- JSX 转换：将 JSX 语法转换为 JavaScript 函数调用

- 代码优化：移除类型注释、压缩代码等

- 浏览器兼容：确保代码在旧版浏览器中正常运行

## 插件(plugin)

插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量。
插件列表：https://webpack.docschina.org/plugins/

插件必须是一个类，通过new方法调用。必须有apply方法。调用Compiler的钩子函数实现功能。

### MiniCssExtractPlugin

对于生产环境，我们通常不会用 style-loader（它会把样式注入到 JS 里），而是使用 mini-css-extract-plugin 对应的 loader。

### HtmlWebpackPlugin

HtmlWebpackPlugin 的主要作用是自动为你生成一个 HTML 文件（通常是 index.html），并自动将 webpack 打包好的 JavaScript 和 CSS 文件（bundle）注入到这个 HTML 文件中。

解决如下问题：

1. 自动创建 HTML 文件
2. 自动注入资源
3. 处理哈希文件名
4. 提供模板系统
5. 优化和压缩

### VueLoaderPlugin

VueLoaderPlugin 是 vue-loader 的一个必需插件，它的主要作用是确保 webpack 能够正确处理 Vue 单文件组件（SFC）中的各种语言块（template、script、style）。

### CssMinimizerWebpackPlugin

它使用 cssnano 来最小化 CSS，移除不必要的字符、注释和空白，从而显著减小 CSS 文件大小。

### SplitChunksPlugin

这是 webpack 中最重要且最强大的优化插件之一。
SplitChunksPlugin 的主要作用是自动将你的代码拆分成多个更小的 chunk（包），以避免重复依赖，并更好地利用浏览器缓存。

自动分析模块的依赖关系，将公共的依赖提取到单独的 chunk 中。

1. 减少了总体积：避免了重复依赖。

2. 优化了缓存策略：公共库变动频率低，可以长期缓存。

3. 提升了加载性能：利用浏览器并行加载机制。

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

## 参考文档

1. https://webpack.docschina.org/configuration/dev-server/#root
2. https://github.com/neutrinojs/webpack-chain

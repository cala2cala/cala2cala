# webpack
webpack 是一个用于现代 JavaScript 应用程序的 静态模块打包工具。

当 webpack 处理应用程序时，它会在内部从一个或多个入口点构建一个 依赖图(dependency graph)，然后将你项目中所需的每一个模块组合成一个或多个 bundles，它们均为静态资源，用于展示你的内容。

## loader

webpack 只能理解 JavaScript 和 JSON 文件，这是 webpack 开箱可用的自带能力。

loader 让 webpack 能够去处理其他类型的文件，并将它们转换为有效 模块，以供应用程序使用，以及被添加到依赖图中。


## 插件(plugin)

插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量。



## webpack经典配置


tree-shaking 

拆包

## chainWebpack用法

webpack-chain 的语法
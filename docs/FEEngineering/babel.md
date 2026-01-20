# babel 是什么

[Babel](https://babeljs.io/docs/config-files) 是一个 JavaScript 编译器。

其主要作用是将采用了 ECMAScript 2015+（ES6+）语法编写的代码，转换为向后兼容的 JavaScript 语法，以便能够运行在当前和旧版本的浏览器或其他环境中。

## 为什么需要 Babel？（解决的问题）

JavaScript 语言的演进：ECMAScript（JS 的标准规范）每年都会发布新版本，引入新的、更强大的语法和功能（如 ES6 的箭头函数、Promise、类等）。

- 环境支持的滞后性：浏览器（如旧版的 IE）、Node.js 等 JavaScript 引擎对这些新特性的支持速度不一致。一些老环境根本无法识别和理解新的语法，直接运行会报错。

- 开发与生产的矛盾：开发者希望使用最新、最简洁、最高效的语法来提升开发体验和代码质量，但产品又需要能在用户可能使用的各种旧环境中稳定运行。

Babel 就是解决这个矛盾的桥梁。它让开发者可以“面向未来编程”，而不用担心环境兼容性问题。

## Babel 是如何工作的？

Babel 的编译过程遵循一个非常清晰的管道模型，主要分为三个步骤：解析 (Parse) -> 转换 (Transform) -> 生成 (Generate)。

### 解析 (Parse)

目的： 将源代码字符串转换成一个抽象语法树 (AST - Abstract Syntax Tree)。

Babel 的解析器（以前是 babylon，现在整合进 @babel/parser）会逐字符读取源代码，并根据 ECMAScript 语法规则对其进行词法分析（Lexical Analysis）和语法分析（Syntax Analysis）。

### 转换 (Transform)

目的： 遍历 AST，并对其中的节点进行修改、删除或新增操作。这是 Babel 最核心、最强大的步骤。

Babel 使用 @babel/traverse 来遍历 AST 的每个节点，同时会应用各种插件 (Plugins) 或预设 (Presets) 来执行具体的转换规则。

### 生成 (Generate)

目的： 将转换后的新 AST 再转换回字符串形式的代码，并创建源码映射 (source map)。

过程：
Babel 使用 @babel/generator 模块来处理这个步骤。它会深度优先地遍历新的 AST，并根据每个节点的信息，递归地拼接成一个完整的代码字符串。

结果：
最终，我们得到了我们想要的、兼容性良好的目标代码字符串。

<!-- TODO 写一个babel插件 -->

## 实战项目中的 babel 配置

```javascript
// babel.config.js

module.exports = {
  // presets：预设，是一组Babel插件的集合
  presets: [
    // 1. TypeScript预设 - 主要功能是移除TypeScript类型注解
    "@babel/preset-typescript",
    // 等价于：
    // ['@babel/preset-typescript', {
    //   // 配置选项
    //   isTSX: true,          // 是否支持TSX语法
    //   allExtensions: true,  // 支持所有扩展名
    //   allowNamespaces: true, // 允许命名空间
    //   allowDeclareFields: true, // 允许类字段声明
    // }]

    // 2. 现代JavaScript语法转换预设
    [
      "@babel/preset-env",
      {
        // 目标环境配置
        targets: {
          browsers: ["> 0.5%", "last 2 versions", "not dead"],
          // 含义：
          // - '> 0.5%': 市场份额大于0.5%的浏览器
          // - 'last 2 versions': 每个浏览器的最近2个版本
          // - 'not dead': 排除不再维护的浏览器
          // 也可以指定具体版本：
          // targets: {
          //   chrome: '58',
          //   ie: '11',
          //   safari: '10',
          //   firefox: '60',
          // }
        },

        // polyfill使用策略：⭐⭐⭐ 核心配置 ⭐⭐⭐
        useBuiltIns: "usage",
        // 可选值：
        // - 'false': 不自动引入polyfill（需要手动引入）
        // - 'entry': 在入口文件顶部引入所有polyfill
        // - 'usage': 按需引入，只引入代码中用到的polyfill（推荐）

        // 指定core-js版本
        corejs: 3,
        // core-js是polyfill的实现库
        // - 2.x: 旧版，功能不全
        // - 3.x: 推荐，包含最新的ES特性

        // 模块转换方式
        modules: false,
        // 可选值：
        // - false: 保留ES模块语法（import/export）
        // - 'commonjs': 转换为CommonJS（require/module.exports）
        // - 'amd', 'umd', 'systemjs' 等
        // 设置为false让Webpack处理模块系统，可以进行Tree Shaking

        // 调试相关配置（可选）
        debug: false, // 设置为true可在控制台查看转换详情

        // 是否包含提案阶段的语法（可选）
        shippedProposals: true, // 包含已进入stage 4的提案

        // 是否转换语法和polyfill分开（可选）
        bugfixes: true, // 启用bug修复模式
      },
    ],
  ],

  // plugins：插件，处理特定的语法转换
  plugins: [
    // 1. Vue 3 JSX支持插件
    "@vue/babel-plugin-jsx",
    // 如果使用JSX语法编写Vue组件需要此插件
    // 等价于：
    // ['@vue/babel-plugin-jsx', {
    //   transformOn: true,      // 转换on: { click: xx } 为 onClick: xxx
    //   optimize: true,         // 启用优化
    //   isCustomElement: tag => tag.startsWith('x-'), // 自定义元素检测
    // }]

    // 2. 类属性提案插件（ES2022类字段语法）
    "@babel/plugin-proposal-class-properties",
    // 支持类中的新语法：
    // class MyClass {
    //   instanceProperty = "value";  // 实例属性
    //   static staticProperty = "static"; // 静态属性
    //   #privateField = "private";   // 私有字段
    // }

    // 3. 可选链操作符插件（ES2020）
    "@babel/plugin-proposal-optional-chaining",
    // 转换：obj?.prop?.nested 为安全的访问链

    // 4. 空值合并操作符插件（ES2020）
    "@babel/plugin-proposal-nullish-coalescing-operator",
    // 转换：value ?? defaultValue

    // 5. Babel运行时插件（重要优化插件）
    [
      "@babel/plugin-transform-runtime",
      {
        // 核心配置：
        corejs: false,
        // - false: 仅使用helpers（默认）
        // - 2: 使用core-js@2的polyfill（不推荐）
        // - 3: 使用core-js@3的polyfill

        helpers: true, // 是否提取helpers函数
        // helpers是Babel注入的辅助函数，如：
        // _classCallCheck, _createClass, _inherits等

        regenerator: true, // 是否使用regenerator-runtime
        // 用于async/await和generator函数的polyfill

        useESModules: false, // 是否使用ES模块格式的helpers
        // 如果targets中支持ES模块可设为true

        version: "^7.14.0", // 指定@babel/runtime版本
      },
    ],

    // 6. 其他常用插件
    // 逻辑赋值运算符（ES2021）
    "@babel/plugin-proposal-logical-assignment-operators",
    // 转换：a ||= b; a &&= b; a ??= b;

    // 顶层await（ES2022）
    "@babel/plugin-syntax-top-level-await",

    // 私有方法（ES2022）
    "@babel/plugin-proposal-private-methods",
  ],

  // 环境特定配置（可选）
  env: {
    // 开发环境配置
    development: {
      // 可以添加开发环境专用插件
      plugins: ["babel-plugin-typescript-to-proptypes"], // 示例
    },
    // 生产环境配置
    production: {
      // 生产环境优化
      plugins: [
        ["transform-remove-console", { exclude: ["error", "warn"] }], // 移除console.log
        "transform-remove-debugger", // 移除debugger
      ],
    },
    // 测试环境配置
    test: {
      presets: [
        [
          "@babel/preset-env",
          {
            targets: {
              node: "current", // 针对当前Node版本
            },
          },
        ],
      ],
    },
  },

  // 其他顶层配置
  assumptions: {
    // 假设目标环境支持某些特性，可以优化输出
    setPublicClassFields: true, // 假设支持公共类字段
    privateFieldsAsProperties: true, // 私有字段作为属性
  },
};
```

@vue/babel-preset-app 是 Vue CLI 创建的项目的默认 Babel 预设。它是一个针对 Vue 应用的官方、优化过的 Babel 预设集合，包含了 Vue 开发所需的所有常用插件。
@babel/preset-env 是 Babel 的核心预设，它能根据你配置的目标环境，智能地决定需要转换哪些 ES6+ 语法，以及需要添加哪些 polyfill。

## Babel + Webpack 合作详解：他们各自做什么？
核心答案：Babel 负责语法转换，Webpack 负责模块打包和资源管理。它们分工合作，缺一不可。




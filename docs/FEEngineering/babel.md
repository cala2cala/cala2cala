# babel是什么

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

<!-- TODO 实战项目中的babel配置 -->

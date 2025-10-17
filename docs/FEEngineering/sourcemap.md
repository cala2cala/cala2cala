# sourcemap

## sourcemap是什么

Source Map（源映射） 是一个独立的文件（通常以 .map 结尾）。

在转换、压缩或合并后的代码与原始的、人类可读的源代码之间建立一种映射关系。

核心目的是：让你在浏览器中调试的，是方便阅读的原始代码，而不是浏览器实际执行的、难以理解的压缩代码。

## 解决了什么问题

前端工程化的过程中，为了优化性能，会对代码进行一系列处理：

1. 压缩（Minification）：移除所有不必要的字符（空格、换行、注释），缩短变量名（如 userAccount -> a）。

2. 混淆（Obfuscation）：故意将代码变得难以理解和逆向工程。

3. 转译（Transpilation）：将高级或特殊语言转换成浏览器兼容的 JavaScript。例如：

- TypeScript -> JavaScript

- ES6+ -> ES5

- Sass/Less -> CSS

- JSX -> JavaScript

4. 打包（Bundling）：将成百上千个模块文件合并成少数几个（甚至一个） bundle 文件以提高网络加载效率。

经过这些处理后，生成的代码执行效率很高，但几乎无法阅读和调试。Source Map 就是为了解决这个“调试困境”而生的，通过sourcemap可以从打包后的文件定位到源文件。

## sourcemap最佳实践

### 开发环境

使用Devtool配置。

#### 以下选项非常适合开发环境：

- eval - 每个模块都使用 eval() 执行，并且都有 //# sourceURL。此选项会非常快地构建。主要缺点是，由于会映射到转换后的代码，而不是映射到原始代码（没有从 loader 中获取 source map），所以不能正确的显示行数。

- eval-source-map - 每个模块使用 eval() 执行，并且 source map 转换为 DataUrl 后添加到 eval() 中。初始化 source map 时比较慢，但是会在重新构建时提供比较快的速度，并且生成实际的文件。行数能够正确映射，因为会映射到原始代码中。它会生成用于开发环境的最佳品质的 source map。

- eval-cheap-source-map - 类似 eval-source-map，每个模块使用 eval() 执行。这是 "cheap(低开销)" 的 source map，因为它没有生成列映射(column mapping)，只是映射行数。它会忽略源自 loader 的 source map，并且仅显示转译后的代码，就像 eval devtool。

- eval-cheap-module-source-map - 类似 eval-cheap-source-map，并且，在这种情况下，源自 loader 的 source map 会得到更好的处理结果。然而，loader source map 会被简化为每行一个映射(mapping)。

### 生产环境

生成并单独保存map文件：构建工具生成 .map 文件，但不上传到公共cdn，而是存到私有cdn上。这是行业最佳实践。既方便开发者在线上环境查看源代码，又设置私有权限保护代码安全。

```javascript
const webpack = require("webpack");
chainWebpack: (config) => {
  if (process.env.VUE_APP_BUILD_ENV === "test") {
    //  哪个环境需要对sourceMap进行分离上传，环境变量对应着写哪个
    config.devtool(false); // 这个是把本地的productionSourceMap给关掉了，用下面的，不关的话，会造成，编译好的js有两个sourceMap的指向（需要注意的地方）
    config
      .plugin("SourceMapDevToolPlugin")
      .use(webpack.SourceMapDevToolPlugin)
      .tap((args) => {
        return [
          {
            filename: "sourcemaps/[file].map", // 生成的sourceMap文件的文件名和包括这些文件的文件名
            publicPath: "http://example/xxx.com/", //  私有化的路径地址，填上你自己的地址，后面的‘/’不要忘记,如果不需要上传到远程地址上，这个字段可以不写，或者写个空字符串都行
            moduleFilenameTemplate: "source-map",
          },
        ];
      });
  }
};
```

分离出来的sourceMap目录，让运维配置命令，部署的时候上传到对应的目录就行了

## 打包sourcemap会影响文件体积吗?

浏览器默认不会加载.map文件，除非打开浏览器开发者工具。

## 参考资料

1. https://juejin.cn/post/7081536160853393438?searchId=20250821112737ED165ECB7F76BABA7524

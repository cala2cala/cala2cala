# 搭建个人博客

## 背景
我之前用hexo和github pages搭建过个人博客，后面没有坚持写下去，大部分文档都写在了公司内网平台上。现在想重新把自己的个人博客捡起来，是用之前搭建的博客还是重新搭建呢？

在公司写的文档，左侧是目录，右侧是文档内容。查找文档方便。之前的博客样式松散，一篇篇的列表下拉，缺少统一的目录，不方便查阅。因此，我打算重新搭建个人博客，系统整理工作中所用到的知识，进行个人沉淀。

### 我对新博客的要求
1. 使用markdown写文档，这样格式统一看起来美观
2. 左侧列表可以嵌套，可折叠，易操作
3. github pages部署

网上搜了下知识库、个人wiki，有人推荐 docsify 。到[官网](https://docsify.js.org/#/zh-cn/)一看，满足我的所有要求，正是我想要的！

## docsify & github pages 搭建个人博客

### 本地 docsify 运行

使用 docsify 搭建博客非常简单，首先本地运行起来。参考官方文档的快速开始即可本地跑起来。

插件配置参考[这篇文档](https://juejin.cn/post/7104301359876276260#heading-10)。

我这里踩了个坑，使用 docsify-sidebar-collapse 实现导航可折叠，需要按照 [docsify-sidebar-collapse 文档](https://github.com/iPeng6/docsify-sidebar-collapse)要求配置_sidebar.md文件。

本地运行地址：http://localhost:3000/#/

### 使用 github pages 托管变成公网可访问的个人博客


GitHub Pages 是 GitHub 提供的 免费静态网站托管服务，允许用户直接从 GitHub 仓库部署静态网页（HTML、CSS、JavaScript），无需服务器配置。它非常适合个人博客、项目文档、作品集等静态网站。

#### GitHub Pages 上，默认情况下：

- 个人/组织主站点（username.github.io）的仓库会自动将内容部署到根目录（https://username.github.io）。

- 项目站点（其他仓库）默认部署在子路径（https://username.github.io/repo-name）。


部署参考[这里](https://docsify.js.org/#/zh-cn/deploy?id=github-pages)。

我的[博客地址](https://cala2cala.github.io/cala2cala/#/)。

至此，一个方便写、查，公网可访问的个人博客就搭建好啦！撒花✿✿ヽ(°▽°)ノ✿

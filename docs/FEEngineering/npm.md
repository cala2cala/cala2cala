# npm

## npm原理
npm 的原理可以概括为：

一个中心化的数据库（注册表package.json） 存储包信息。

一个客户端工具 负责与数据库通信、解析依赖关系、下载和管理包。

一个本地目录（node_modules） 存放所有下载的代码。

一个依赖解析算法 来决定 node_modules 的结构（从嵌套到扁平化）。

一个锁定文件（package-lock.json） 来保证依赖树的一致性，实现“一次安装，处处运行”。

与 Node.js 的 require 机制紧密配合，使得模块能够被正确找到和加载。

## yarn和npm区别
你正在维护一个 Monorepo，Yarn Workspaces 的成熟度可能更高。

你更喜欢 Yarn 的命令行体验和输出日志。

你的团队或项目历史原因一直在使用 Yarn，并且运行良好。

你欣赏 Yarn 在安全性上的一些前瞻性设计。

### 什么是 Yarn Workspaces？
Yarn Workspaces 是 Yarn 提供的一项功能，它允许你在一个顶级根包（通常是一个仓库）中管理多个子包（子项目）。它能够：

- 自动链接依赖：如果 Workspace A 依赖 Workspace B，Yarn 会自动创建一个符号链接，而不是从 npm 注册表下载，让你在本地直接开发测试。

- 统一安装依赖：你只需要在根目录运行一次 yarn install，Yarn 就会为所有 Workspace 安装所有依赖项。

- 共享 node_modules：Yarn 会尽可能地将依赖提升到根目录的 node_modules 中，避免重复安装，节省磁盘空间和安装时间。

- 跨包脚本执行：可以方便地在根目录运行所有或特定子包的脚本。

## pnpm和npm区别

### npm存在的问题
幽灵依赖： 你可以在代码中直接 require('package-a')，即使它不在你的 package.json 的 dependencies 中，仅仅因为它是另一个依赖的子依赖并被提升到了顶层。这非常危险，一旦那个依赖不再依赖 package-a，你的代码就会报错。

###  pnpm（内容可寻址存储 + 硬链接）：
- pnpm 在全局有一个存储中心（通常在 ~/.pnpm-store）。

- 当你安装一个包时，pnpm 会：

1. 将包的文件下载到全局存储中。

2. 在项目的 node_modules 中，只创建硬链接指向全局存储中的文件。

- 项目的 node_modules 结构是 嵌套的、符合依赖关系的，但同时又非常快速和节省空间。

## npx
npx 是一个工具，它让你能非常方便地运行 Node.js 生态系统中（通过 npm 发布的）的包，而无需先全局安装它们。

可以把 npx 理解为 “npm 包的执行器”。

### 为什么需要 npx？解决什么问题？

在 npx 出现之前，如果你想在命令行里运行一个工具（比如 create-react-app），你必须先全局安装它：

```javascript
# 旧的方式（现在不推荐了）
npm install -g create-react-app  # 全局安装
create-react-app my-app          # 然后才能使用
``` 

这种方式有几个问题：

占用空间：你可能只用一两次的工具，却永久地安装在了你的电脑上。

版本冲突：如果你不同的项目需要同一个工具的不同版本，全局安装只能有一个版本，这会导致冲突。

不够便捷：对于只是想快速尝试一个工具的场景，步骤太繁琐。

npx 完美地解决了这些问题：

```javascript
# 现代的方式（推荐）
npx create-react-app my-app
``` 

当你运行这行命令时，npx 会做以下几件事：

检查本地项目（./node_modules/.bin 路径）或全局安装中是否已经存在 create-react-app。

如果找不到，它会自动从 npm 仓库下载该包的最新版本。

运行这个包。

运行结束后，下载的包会被清理掉，不会永久保存在你的电脑上。

## npx 的主要用途
- 运行脚手架工具（最常见）：创建新项目。
```javascript
npx create-react-app my-app （创建 React 项目）
```
<!-- // npx create-vite\@latest my-app （创建 Vite 项目，\@\latest 指定最新版） -->

<!-- // npx degit user\/repo my-project （使用 degit 克隆仓库） -->
- 快速运行、测试某个包：想临时试试一个工具，比如一个代码检查器、一个 HTTP 服务器。
```javascript
npx http-server （快速启动一个静态文件服务器）
```
<!-- // npx cowsay "Hello World" （让一头牛说 Hello World）-->

- 运行不同版本的包：轻松测试一个包在不同版本下的行为。
```javascript
npx node@14 --version （临时使用 Node.js 14 来查看版本）
``` 
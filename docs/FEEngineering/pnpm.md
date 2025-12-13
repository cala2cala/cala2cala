# 使用 pnpm 管理 monorepo

## monorepo 适合场景

Monorepo（单一代码仓库）能显著提升开发效率和代码质量，但也带来一定的管理复杂度。
适合以下主要场景：

1. 高度关联的多项目/多包系统
   典型场景：企业级产品套件（如阿里 Ant Design、字节 Arco Design）、微前端架构。

解决的问题：多个项目紧密耦合，需要频繁共享和同步代码。传统多仓库（Multirepo）下，跨仓库更新依赖、同步 API 变更、统一版本发布极其繁琐。

2. 需要严格依赖管理和版本一致性
   典型场景：大型应用拆分的功能包、共享工具库和组件库。

解决的问题：确保所有项目使用完全相同的第三方依赖版本（如 React、TypeScript）或内部工具库版本，避免因版本差异导致的“在我的机器上能运行”问题。Monorepo 可使用单个 lock 文件锁定所有依赖。

## pnpm 管理 monorepo

pnpm 是针对 npm/yarn 在 Monorepo 场景下磁盘空间浪费、依赖关系混乱、安装速度慢等痛点的一次有效革新，这也是它越来越多地被大型项目（如 Vue3、Vite）采用的原因。

### pnpm workspace

PNPM Workspace 是一个 monorepo 管理工具，让你在单个代码仓库中管理多个项目/包，同时：

✅ 共享依赖（避免重复安装）

✅ 包之间可以相互引用

✅ 统一版本管理和发布

✅ 高效的磁盘空间使用

在根目录配置 workspace。

### pnpm 新增外部依赖

```javascript
pnpm add lodash -w // 公共依赖
pnpm add lodash --filter vue-demo1 // 子项目依赖
```

-w, --workspace-root 运行起来就好像 pnpm 是在 工作空间 的根目录中启动的，而不是在当前工作目录中启动的。

### pnpm 卸载依赖

```javascript
pnpm remove lodash -w // 公共依赖
pnpm remove lodash --filter vue-demo1 // 子项目依赖
```

### pnpm 新增共享子包

```javascript
pnpm add @libc/shared --workspace -w
```

--workspace 尝试从工作空间链接所有软件包。 版本将更新至与工作空间内的包匹配的版本。

### pnpm 过滤操作

```javascript
# 只在特定包运行命令
pnpm --filter <package-name> <command>
```

### pnpm 批量操作

```javascript
# 在所有包中运行命令
pnpm -r <command>
```

### pnpm 查看 workspace 包之间的依赖关系

```javascript
pnpm -r list
```

可以在[官网](https://pnpm.io/zh/)查询的作用，或者使用 --help 命令。
[测试 demo](https://github.com/cala2cala/pnpm-monorepo)。

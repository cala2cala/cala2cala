# yarn Workspace

```javascript
# 只安装某个包的依赖
yarn workspace @my/ui install

# 添加根目录依赖（工具类）
yarn add -W typescript eslint
```


之前的项目用yarn Workspace 管理monorepo，用lerna version 和 publish


https://github.com/Hy-Vee/lerna-yarn-workspaces-monorepo


问题：monorepo下的依赖如何管理最好？

目前业界使用最多的方案是：lerna + yarm workspace 结合的 Monorepo 方案，两者工作职责划分不同：

yarn 处理依赖管理工作（只想做好包管理工具）；
lerna 处理发布流程。

https://juejin.cn/post/7097820725301477406?searchId=2025121321164814AB747D7A21A3A15D70#heading-9
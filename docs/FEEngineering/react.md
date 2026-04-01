## 问题

### npm i 卡住怎么办，2次都失败了

使用国内镜像源 npm config set registry https://registry.npmmirror.com

### 从github上拉下来的项目，为什么npm i 后package-lock.json会更新，package-lock.json文件也是git从远端拉下来的

我看了下大概是依赖的版本更新了，符合了其他依赖的条件，因此新装了这个依赖。

### 项目的入口代码是啥？

ReactDOM.createRoot(element as HTMLDivElement,{}).render(tsx)
tsx：

1. 首先用react的StrictMode包一下，作用是在开发模式中启用组件树内部的额外开发行为和警告：
2. 然后用antd的App组件包，提供重置样式和提供消费上下文的默认环境。
3. I18nextProvider包一下，提供国际化
4. 包一个无dom的组件，是一个监听屏幕尺寸变化并同步到全局状态的组件，主要用于处理响应式布局。
5. 并列的ErrorBoundary 是错误渲染组件
6. Suspense由react提供，<Suspense> 允许在子组件完成加载前展示后备方案。
7. VercelInsights
8. 主题 dark light
9. 水印
10. StyleProvider antd 以规避静态方法对样式的影响。
11. ConfigProvider antd为组件提供统一的全局化配置。
12. react-router-dom引入RouterProvider
13. 被秀了下keepAliveManager实现，通过LRU机制，将当前组件移出视口
14. 路由守卫

### useEffect

Effect 允许你指定由渲染自身，而不是特定事件引起的副作用。默认情况下，Effect 会在 每次 渲染后运行。但往往 这并不是你想要的,因此需要增加依赖项。
当依赖项是空数组 [] 时，useEffect 只在组件挂载后执行一次，后续重新渲染不会再次执行。
useEffect 的回调函数不能是 async 函数
返回组件卸载时自动执行的代码，用来清理副作用。

### useState

state的改变会触发重新渲染

### useRef
useRef 是一个 React Hook，它能帮助引用一个不需要渲染的值。

改变 ref 不会触发重新渲染。
使用 ref 可以确保：

可以在重新渲染之间 存储信息（普通对象存储的值每次渲染都会重置）。
改变它 不会触发重新渲染（状态变量会触发重新渲染）。
对于组件的每个副本而言，这些信息都是本地的（外部变量则是共享的）。

### 立即执行的异步函数表达式

// 立即执行的异步函数表达式
(async () => {
const ok = await canUseVercelInsights()
if (!cancelled) setEnabled(ok)
})() // 立即执行

### createContext

上下文使得组件能够无需通过显式传递参数的方式 将信息逐层传递。

### useMemo

useMemo 来缓存每次重新渲染都需要计算的结果。在组件的顶层 或者自定义 Hook 中调用它。
useMemo 是 React 的性能优化 Hook，用于缓存计算结果，只在依赖项变化时重新计算。
useMemo 是优化工具，不是必须的语法。

当依赖项是空数组，只在组件首次渲染时创建路由对象，永远不会重新计算（因为依赖项永远不会变化），路由配置在整个应用生命周期中保持不变。

### return <>{children}</>

不加任何额外的 div 或包装元素

### requestAnimationFrame隐藏loading
requestAnimationFrame 的回调会在下一帧开始前、浏览器执行渲染前执行。

使用双重 rAF 确保在下一帧的布局和绘制之后执行
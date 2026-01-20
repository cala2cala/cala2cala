# Rollup VS Webpack VS esbuild

## esbuild 确实自己实现了编译功能，但：

不是 Babel 的替代品，而是不同的技术路线

追求极致的速度，牺牲了灵活性和兼容性

适合现代开发，但不适合需要广泛兼容的场景

未来趋势：Vite、Turbopack 等工具都在采用类似思路

## 现代项目的最佳实践：

开发时：用 esbuild（极速热更新）

生产构建：根据目标浏览器决定

现代浏览器：esbuild

旧浏览器：Rollup + Babel

渐进策略：先用 esbuild，必要时补充 Babel

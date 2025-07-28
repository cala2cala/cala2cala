# 网页性能优化

## 什么是性能优化？

前端页面是用户直接接触的产品界面，页面内容加载的速度和使用的流畅度会直接影响到用户的体验。如果页面打开长时间白屏，用户会失去耐心而关闭页面；用户操作反复提示失败，用户会不耐烦关闭页面；动画卡顿，看起来不舒服，用户也会关闭页面。失去了用户，产品如何创造价值呢？因此，提升用户的使用体验，减少体验问题，是性能优化的目标。


## 性能优化的指标

我们需要一些指标来衡量网页性能。


### 核心性能指标
Google 提出的 Core Web Vitals 是当前最常用的标准，涵盖用户体验的三大关键维度：
- Largest Contentful Paint (LCP)：衡量加载性能。为了提供良好的用户体验，请尽力在网页开始加载的 2.5 秒内完成 LCP。
- Interaction To Next Paint (INP)：衡量响应速度。为了提供良好的用户体验，请尽力将 INP 控制在 200 毫秒以内。
- Cumulative Layout Shift (CLS)：衡量视觉稳定性。为了提供良好的用户体验，请尽力使 CLS 得分低于 0.1。

### 其他性能指标

#### 加载阶段指标
- FP（First Paint，首次绘制）

浏览器首次渲染任何像素（如背景色）。

- FCP（First Contentful Paint，首次内容绘制）

- 首次显示文本、图片等实际内容的时间。

- TTI（Time to Interactive，可交互时间）

页面完全可交互的时间（JS 已加载、主线程空闲）。

优化方向：减少第三方脚本、延迟非关键 JS。

#### 渲染与交互性能
- FPS（Frames Per Second，帧率）

动画或滚动时的流畅度（目标 ≥60 FPS）。

优化方向：

减少复杂 CSS 选择器或频繁 DOM 操作。

使用 requestAnimationFrame 替代 setTimeout。


## 如何发现和定位性能问题？
解决问题的第一步是发现问题。

### 本地性能检测工具
Lighthouse（Chrome DevTools）：综合评分。

WebPageTest：多地点测试加载速度。

Chrome Performance Tab：分析运行时性能。

### 持续监控（RUM）
Google Analytics：统计真实用户加载时间。

Sentry：监控页面卡顿和错误。


## 优化网页性能
- 网络层	CDN、HTTP/2、压缩、缓存
- 资源加载	懒加载、预加载、异步加载
- 渲染优化	减少重排、RAF、GPU 加速
- 代码优化	Tree Shaking、Worker、内存管理

性能优化涉及到方方面面，后续将详细介绍工具的使用和各方面详细内容及实际案例。


## 参考链接
https://w3c.github.io/paint-timing/
https://w3c.github.io/largest-contentful-paint/
https://docs.google.com/document/d/1BR94tJdZLsin5poeet0XoTW60M0SjvOJQttKT-JK8HI/view?pli=1&tab=t.0#heading=h.tdqghbi9ia5d
https://developer.mozilla.org/zh-CN/docs/Web/API/PerformanceNavigationTiming
https://developers.google.com/search/docs/appearance/core-web-vitals?hl=zh-cn
https://web.dev/articles/vitals?hl=zh-cn#core-web-vitals
https://developer.mozilla.org/zh-CN/docs/Web/API/PerformanceNavigationTiming
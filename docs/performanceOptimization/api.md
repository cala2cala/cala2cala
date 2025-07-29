# 网页性能指标

## 性能指标定义

### LCP (Largest Contentful Paint) - 最大内容绘制

#### LCP测量的是从页面开始加载到可视区域（视口viewport）内最大内容元素完成渲染的时间点。这个"最大内容元素"通常是：

- 图片（包括<img>标签和CSS背景图）

- <video>元素

- 包含文本的块级元素（如段落、标题）

- SVG元素

- 带有背景图片的元素

#### LCP的重要性
- 用户体验指标：反映用户实际看到主要内容的时间

- SEO影响因素：Google明确将LCP作为搜索排名因素

- 业务指标相关：更好的LCP通常带来更高的转化率


#### LCP的评分标准
| LCP时间   | 评分等级          |
|:-------|-----:|
| ≤2.5秒  | 良好          |
| 2.5-4秒    | 需要改进        |
| >4秒  | 22   |较差
	
	
	
	

### FID (First Input Delay) - 首次输入延迟

### CLS (Cumulative Layout Shift) - 累积布局偏移

### FCP (First Contentful Paint) - 首次内容绘制

### TTFB (Time to First Byte) - 首字节时间

### TTI (Time to Interactive) - 可交互时间



## 测量性能指标

- Performance Timeline API

performance.now() - 高精度时间戳

performance.getEntries() - 获取所有性能条目

- PerformanceObserver - 性能观察器（现代推荐用法）

Navigation Timing API

performance.timing (已废弃)

- PerformanceNavigationTiming - 提供页面导航和加载的详细时间信息

Resource Timing API

- PerformanceResourceTiming - 跟踪所有资源的加载时间（脚本、图片、CSS等）

User Timing API

performance.mark() - 创建自定义时间点标记

performance.measure() - 测量两个标记点之间的时间

Paint Timing API

First Paint (FP)

First Contentful Paint (FCP)


https://juejin.cn/post/7429128606749949978?searchId=202507281246192952343C5DB60A0FB795#heading-23

https://w3c.github.io/paint-timing/
https://w3c.github.io/largest-contentful-paint/
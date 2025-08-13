# 网页性能分析工具

## Chrome DevTools

- Performance 面板：记录和分析运行时性能
运行时详细性能分析，开发中性能问题定位。

- Lighthouse：全面的性能审计工具
从性能、无障碍功能、最佳做法、SEO4个维度进行评估，并给出具体的优化建议

- Network 面板：分析网络请求和加载时间

- Coverage 面板：检测未使用的CSS/JS代码
在network网页下看JS文件代码覆盖率

### Performance 面板用法详解
Performance面板(原Timeline面板)是浏览器开发者工具中用于分析页面运行时性能的强大工具。

打开Chrome devtool，切换到performance面板，可以看到当前页面的主要性能指标。
![这是图片](https://raw.githubusercontent.com/cala2cala/image-hosting/main/wechat_2025-08-13_175957_968.png "performance面板")

点击「录制」或者「录制并重新加载」，稍等片刻，点击按钮结束录制，能看到性能分析结果。

#### 面板区域解析
录制完成后，面板分为几个主要区域：

##### 概览(Overview)：

FPS：帧率，绿色越高越好，红色表示卡顿

CPU：CPU使用情况，不同颜色代表不同任务类型

NET：网络请求情况

##### 火焰图(Flame Chart)：

主线程活动详情，显示JavaScript执行、渲染、布局等事件

可缩放查看细节(使用鼠标滚轮或触控板)

##### 统计摘要(Summary)：

按类型分类的活动时间统计

包括Loading、Scripting、Rendering、Painting等

#### 快捷操作
- 放大（Zoom In）：W
- 缩小（Zoom In）：S
- 平移时间轴，向左移动：A
- 平移时间轴，向右移动：D

### 谷歌官方示例
以Google官方示例为例，仓库地址:[点击这里](https://github.com/GoogleChrome/devtools-samples/tree/main/jank).

页面访问地址：[点击这里](https://googlechrome.github.io/devtools-samples/jank/)。

打开页面后，点击「Add 10」按钮，到页面动画明显卡顿后，记录性能，结果如下图。

可以看到概览区域有红色，表示有掉帧卡顿。


![这是图片](https://raw.githubusercontent.com/cala2cala/image-hosting/main/wechat_2025-08-13_202058_104.png "performance面板")

点开「主要」，有的任务右上角有红标，说明是长任务。选中，「自下而上」里可以看到执行细节。

![这是图片](https://raw.githubusercontent.com/cala2cala/image-hosting/main/wechat_2025-08-13_202951_034.png "performance面板")

点进去可以看到具体代码的执行细节以及执行时长。这样就具体定位到了问题，从而针对性解决。
![这是图片](https://raw.githubusercontent.com/cala2cala/image-hosting/main/微信图片_2025-08-13_203031_454.png "performance面板")

### 长任务定义

### 长任务怎么处理TODO
涉及到浏览器渲染过程


## 在线分析工具

- WebPageTest (https://www.webpagetest.org/)

多地点测试

视频录制

详细的水滴图分析
- PageSpeed Insights (https://developers.google.com/speed/pagespeed/insights/)TODO打不开

谷歌提供的性能分析

移动和桌面分别评分


##  性能监控工具

在公司用的是基于web-vitals封装的自研apm平台。


## 参考链接
- https://developer.chrome.com/docs/devtools/?hl=zh-cn
- https://web.dev/articles/optimize-long-tasks?utm_source=devtools&utm_campaign=stable&hl=zh-cn
- https://web.dev/articles/avoid-large-complex-layouts-and-layout-thrashing?utm_source=devtools&utm_campaign=stable&hl=zh-cn#avoid-forced-synchronous-layouts
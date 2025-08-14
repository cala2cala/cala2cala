# 优化网页性能
- 网络层	CDN、HTTP/2、压缩、缓存
- 资源加载	懒加载、预加载、异步加载
- 渲染优化	减少重排、RAF、GPU 加速
- 代码优化	Tree Shaking、Worker、内存管理

## 网络层性能优化

### CDN（Content Delivery Network，内容分发网络）
旨在通过在全球多个地理位置部署服务器（边缘节点），将内容快速、高效地分发给用户，从而提升访问速度、降低延迟，并减轻源站服务器的负载。

#### CDN 的核心原理
- 边缘节点缓存

CDN 将静态内容（如 HTML、CSS、JS、图片、视频）缓存到全球各地的边缘服务器上。

用户访问时，直接从最近的节点获取数据，而非每次都从源站拉取，减少传输距离。

- 智能 DNS 解析

当用户请求资源时，CDN 的 DNS 系统会根据用户的地理位置，返回最优边缘节点的 IP 地址，而非源站 IP。

- 负载均衡

CDN 自动分配用户请求到不同的节点，避免单个服务器过载，提高稳定性。

- 动态加速（可选）

对于动态内容（如 API、实时数据），CDN 会优化传输路径（如 BGP Anycast、TCP 优化），降低延迟。

### HTTP/2
HTTP/2 通过以下核心技术显著加快网络请求速度，相比 HTTP/1.x 有本质提升：
- 多路复用（Multiplexing）
- 二进制分帧层（Binary Framing）
- 头部压缩（HPACK）
- 服务器推送（Server Push）
- 流优先级（Stream Prioritization）

可以看到下方请求都是HTTP/2。
![这是图片](https://raw.githubusercontent.com/cala2cala/image-hosting/main/wechat_2025-08-14_101039_025.png "Magic Gardens")


### 压缩
能显著减少传输数据量，降低带宽消耗，从而加快页面加载速度。

主流方法是构建阶段压缩：在打包时（如 Webpack、Vite、Rollup）直接生成压缩文件，减少服务器实时压缩的压力。
压缩文件类型：
- JavaScript 
- CSS
- HTML
- 图片
- 字体文件

压缩实现方法，参见webpack配置与原理。TODO

### 减少请求数量
合并文件

### 缓存
#### 浏览器缓存（客户端缓存）
利用 HTTP 缓存机制，将静态资源（如 JS/CSS/图片）存储在用户本地，避免重复下载。
##### 强缓存（无需服务器验证）
现代浏览器（HTTP/1.1+）优先遵循 Cache-Control，而 Expires 是 HTTP/1.0 的旧标准，仅作为备用。

```javascript
cache-control:max-age=15552000 //缓存半年
```

##### 协商缓存（需服务器验证）
通过 Last-Modified（时间戳）或 ETag（文件哈希）判断资源是否修改。
```javascript
etag:"70f7788e3af3c938d64ecc21a5fb29af"
last-modified:Mon, 21 Jul 2025 03:31:51 GMT
```
若未修改，服务器返回 304 Not Modified，节省带宽。

#### 服务端缓存（服务器级缓存）
在服务器内存或 Redis 中缓存动态内容（如数据库查询结果）。

## 资源加载

### 懒加载
不立即加载所有资源，而是等到用户需要时（如滚动到可视区域）再加载。

#### 懒加载的最佳实践

- 优化图片加载
- 优化长列表（无限滚动）
-  非关键 JS 延迟加载


### 预加载
#### 资源预加载(Resource Preloading)
```javascript
<link rel="preload" href="font.woff2" as="font" type="font/woff2" crossorigin>
```

#### DNS 预解析(DNS Prefetching)
只预加载真正关键的资源，避免浪费带宽。

```javascript
<link rel="dns-prefetch" href="//example.com">
```
#### 预连接(Preconnect)
```javascript
<link rel="preconnect" href="https://cdn.example.com" crossorigin>
```
#### 预获取(Prefetch)
```javascript
<link rel="prefetch" href="/next-page.html" as="document">
```


## 网渲染优化

### 减少重排
重排（Reflow）是浏览器重新计算元素位置和几何信息的过程，会显著影响页面性能。

- 集中修改DOM
- 使用CSS类批量修改样式
- 使用CSS3 transform和opacity：这些属性不会触发重排，因为它们是在合成层处理的。
- 使用Flexbox或Grid布局
- 将频繁重排的元素设置为独立图层：will-change: transform; /* 提示浏览器提前优化 */
- 避免表格布局
- 使用虚拟列表处理长列表
- 使用requestAnimationFrame。

### RAF
requestAnimationFrame 告诉浏览器你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。

### GPU 加速
GPU加速是利用图形处理单元来提升网页渲染性能的技术，可以显著改善动画、过渡和复杂视觉效果的流畅度。
```css
/* 使用这些属性会触发GPU加速 */
.element {
  transform: translateZ(0); /* 或 translate3d(0,0,0) */
  backface-visibility: hidden;
  perspective: 1000px;
  will-change: transform, opacity; /* 提前告知浏览器可能的变化 */
}
```
优先使用transform和opacity：这些属性可以由GPU高效处理

避免使用left/top/margin等属性：这些会触发重排(reflow)

## 代码优化

### Tree Shaking
用于消除项目中未使用的代码，从而减小最终打包文件的体积。

通过静态分析来识别和删除未引用代码的优化技术。

TODO具体实现参见webpack配置。

### Web Worker
#### 合理使用场景
- CPU 密集型计算：图像处理、复杂算法、大数据分析

- 长时间运行任务：避免阻塞主线程导致页面卡顿

- 后台处理：日志上报、数据预处理等不影响主线程的任务

# TODO 前端工程中（webpack、vue.js）的性能优化
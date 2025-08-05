# 前端代码测量性能指标
## 代码实现
PerformanceObserver是专门用来测量网页性能的api。


### LCP (Largest Contentful Paint) - 最大内容绘制

```JavaScript
const observer = new PerformanceObserver((entryList) => {
  const entries = entryList.getEntries(); // 获取所有性能条目
  const lastEntry = entries[entries.length - 1]; // 取最新的 LCP 记录
  
  console.log("LCP:", lastEntry.renderTime || lastEntry.loadTime);
  console.log("LCP Element:", lastEntry.element); // 关联的 DOM 元素
});
// 开始观察 LCP
observer.observe({ type: "largest-contentful-paint", buffered: true });
```

### FCP (First Contentful Paint) - 首次内容绘制
```JavaScript
// 创建 PerformanceObserver 监听 "paint" 类型条目
const observer = new PerformanceObserver((list) => {
  const entries = list.getEntriesByName('first-contentful-paint');
  
  if (entries.length > 0) {
    const fcp = entries[0];
    console.log('FCP:', fcp.startTime, 'ms');
    console.log('FCP Element:', fcp.element || '无关联元素');
    
    // 停止监听（避免重复上报）
    observer.disconnect();
  }
});

// 监听 "paint" 事件（FCP 是一种特殊的 paint 事件）
observer.observe({ type: 'paint', buffered: true });
```

### FID (First Input Delay) - 首次输入延迟
```JavaScript
let fidReported = false;

// 监听首次输入事件
const onFirstInput = (entry) => {
  if (fidReported) return;
  fidReported = true;

  const delay = entry.processingStart - entry.startTime;
  console.log('FID:', delay, 'ms');

  // 上报逻辑
  // sendToAnalytics({ fid: delay });
};

// 使用 PerformanceObserver 捕获 "first-input" 条目
const observer = new PerformanceObserver((list) => {
  list.getEntries().forEach(onFirstInput);
});

observer.observe({ type: 'first-input', buffered: true });

// 兜底：如果 PerformanceObserver 未捕获，用事件监听器
window.addEventListener('click', (e) => {
  if (fidReported) return;
  fidReported = true;

  const now = performance.now();
  const delay = now - e.timeStamp;
  console.log('Fallback FID:', delay, 'ms');
}, { once: true, capture: true });
```
### TTI (Time to Interactive) - 可交互时间
#### TTI 的算法逻辑（参考 Lighthouse）：

- 找到 FCP（First Contentful Paint）。

- 向后搜索 5s 静默窗口（无长任务阻塞主线程）。

- 确认之前的网络请求 ≤ 2 个。
```JavaScript
function calculateTTI() {
  return new Promise((resolve) => {
    const fcpEntry = performance.getEntriesByName('first-contentful-paint')[0];
    if (!fcpEntry) {
      console.warn('FCP not found');
      return resolve(null);
    }

    const fcpTime = fcpEntry.startTime;
    const longTasks = performance.getEntriesByType('long-task');
    const networkRequests = performance.getEntriesByType('resource');

    // 找到 FCP 后第一个 5s 静默窗口
    let tti = fcpTime;
    for (const task of longTasks) {
      if (task.startTime < fcpTime) continue;
      if (task.startTime - tti >= 5000) break; // 5s 无长任务
      tti = task.startTime + task.duration;
    }

    // 验证网络请求 ≤ 2 个
    const requestsAfterTti = networkRequests.filter(
      req => req.startTime <= tti && req.responseEnd >= tti
    ).length;

    if (requestsAfterTti <= 2) {
      console.log('TTI:', tti, 'ms');
      resolve(tti);
    } else {
      console.warn('TTI calculation failed: too many network requests');
      resolve(null);
    }
  });
}

// 在页面加载完成后计算
window.addEventListener('load', () => {
  setTimeout(calculateTTI, 1000); // 等待长任务可能结束
});
TODO 没看懂这个是怎么计算的？
```
## 使用现成库
使用[web-vitals](https://github.com/GoogleChrome/web-vitals)获取性能数据。

```JavaScript
import {getCLS, getFID, getLCP, getFCP, getTTFB} from 'web-vitals';

// 分别获取各项指标
getCLS(console.log);
getFID(console.log);
getLCP(console.log);
getFCP(console.log);
getTTFB(console.log);
```


## Performance API 监测能力概览
- 导航计时 (Navigation Timing)
- 资源计时 (Resource Timing)
- 用户自定义计时 (User Timing)
- 绘制性能 (Paint Timing)
- 长任务监测 (Long Task API)
- 布局偏移监测 (Layout Instability API)
- 首字节时间 (TTFB)
- 元素计时 (Element Timing)
- 服务器计时 (Server Timing)
- 内存使用情况
# 浏览器 核心概念与面试题

## 1. 浏览器渲染原理

### 核心概念
- **渲染流程**：HTML解析 → CSS解析 → 布局 → 绘制 → 合成
- **DOM树**：HTML文档的对象模型
- **CSSOM树**：CSS样式表的对象模型
- **渲染树**：DOM树和CSSOM树的结合
- **重排重绘**：布局和绘制的重新计算

### 常问面试题

#### Q1: 浏览器渲染流程是什么？
**答案：**
```
浏览器渲染流程：

1. HTML解析
   - 解析HTML文档，构建DOM树
   - 遇到CSS链接时，异步下载CSS文件
   - 遇到JavaScript时，可能阻塞渲染

2. CSS解析
   - 解析CSS文件，构建CSSOM树
   - 计算每个节点的样式

3. 渲染树构建
   - 将DOM树和CSSOM树结合
   - 只包含可见元素

4. 布局（Layout/Reflow）
   - 计算每个元素的位置和大小
   - 确定元素在页面中的确切位置

5. 绘制（Paint）
   - 填充像素，绘制每个元素
   - 包括文本、颜色、图像、边框等

6. 合成（Composite）
   - 将各层合并，最终显示在屏幕上
   - 利用GPU加速合成层

优化建议：
- 减少重排重绘
- 使用transform和opacity
- 避免频繁改变样式
- 使用will-change提示浏览器
```

#### Q2: 什么是重排和重绘？
**答案：**
```javascript
// 重排（Reflow）- 改变元素位置和大小
function causeReflow() {
  const element = document.getElementById('myElement');
  
  // 这些操作会触发重排
  element.style.width = '200px';
  element.style.height = '100px';
  element.style.left = '50px';
  element.style.top = '50px';
  
  // 获取布局信息也会触发重排
  const width = element.offsetWidth;
  const height = element.offsetHeight;
}

// 重绘（Repaint）- 改变元素外观
function causeRepaint() {
  const element = document.getElementById('myElement');
  
  // 这些操作会触发重绘
  element.style.color = 'red';
  element.style.backgroundColor = 'blue';
  element.style.border = '1px solid black';
}

// 优化：使用transform避免重排
function optimizedAnimation() {
  const element = document.getElementById('myElement');
  
  // 使用transform只触发合成层，不触发重排
  element.style.transform = 'translateX(100px)';
  element.style.opacity = '0.5';
}

// 批量DOM操作
function batchDOMOperations() {
  const fragment = document.createDocumentFragment();
  
  for (let i = 0; i < 1000; i++) {
    const div = document.createElement('div');
    div.textContent = `Item ${i}`;
    fragment.appendChild(div);
  }
  
  document.body.appendChild(fragment); // 只触发一次重排
}
```

## 2. 事件循环机制

### 核心概念
- **调用栈**：执行JavaScript代码的栈
- **任务队列**：宏任务队列
- **微任务队列**：Promise、queueMicrotask
- **事件循环**：不断检查队列并执行任务
- **异步执行**：非阻塞的代码执行

### 常问面试题

#### Q3: JavaScript事件循环机制？
**答案：**
```javascript
// 事件循环执行顺序
console.log('1'); // 同步代码

setTimeout(() => {
  console.log('2'); // 宏任务
}, 0);

Promise.resolve().then(() => {
  console.log('3'); // 微任务
});

console.log('4'); // 同步代码

// 输出顺序：1, 4, 3, 2
// 解释：同步代码 → 微任务 → 宏任务

// 详细示例
console.log('开始');

setTimeout(() => console.log('setTimeout 1'), 0);
setTimeout(() => console.log('setTimeout 2'), 0);

Promise.resolve().then(() => {
  console.log('Promise 1');
  return Promise.resolve();
}).then(() => {
  console.log('Promise 2');
});

queueMicrotask(() => {
  console.log('queueMicrotask');
});

console.log('结束');

// 输出顺序：
// 开始
// 结束
// Promise 1
// queueMicrotask
// Promise 2
// setTimeout 1
// setTimeout 2

// 宏任务和微任务
// 宏任务：setTimeout, setInterval, I/O操作, UI渲染
// 微任务：Promise.then, queueMicrotask, MutationObserver
```

## 3. 浏览器缓存

### 核心概念
- **HTTP缓存**：浏览器缓存机制
- **强缓存**：Cache-Control、Expires
- **协商缓存**：ETag、Last-Modified
- **Service Worker**：离线缓存
- **本地存储**：localStorage、sessionStorage

### 常问面试题

#### Q4: 浏览器缓存策略？
**答案：**
```javascript
// 1. 强缓存
// 服务器设置响应头
app.get('/api/data', (req, res) => {
  res.setHeader('Cache-Control', 'max-age=3600'); // 1小时
  res.setHeader('Expires', new Date(Date.now() + 3600000).toUTCString());
  res.json({ data: 'cached data' });
});

// 2. 协商缓存
app.get('/api/data', (req, res) => {
  const etag = 'etag-value';
  const lastModified = new Date().toUTCString();
  
  // 检查ETag
  if (req.headers['if-none-match'] === etag) {
    return res.status(304).end();
  }
  
  // 检查Last-Modified
  if (req.headers['if-modified-since'] === lastModified) {
    return res.status(304).end();
  }
  
  res.setHeader('ETag', etag);
  res.setHeader('Last-Modified', lastModified);
  res.json({ data: 'fresh data' });
});

// 3. Service Worker缓存
self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request).then(response => {
      if (response) {
        return response; // 返回缓存
      }
      
      return fetch(event.request).then(response => {
        // 缓存新请求
        const responseClone = response.clone();
        caches.open('v1').then(cache => {
          cache.put(event.request, responseClone);
        });
        return response;
      });
    })
  );
});

// 4. 本地存储
// localStorage - 持久化存储
localStorage.setItem('key', 'value');
const value = localStorage.getItem('key');
localStorage.removeItem('key');
localStorage.clear();

// sessionStorage - 会话存储
sessionStorage.setItem('key', 'value');
const value = sessionStorage.getItem('key');

// 5. 缓存策略
const cacheStrategies = {
  // 缓存优先
  cacheFirst: async (request) => {
    const cached = await caches.match(request);
    if (cached) return cached;
    
    const response = await fetch(request);
    const cache = await caches.open('v1');
    cache.put(request, response.clone());
    return response;
  },
  
  // 网络优先
  networkFirst: async (request) => {
    try {
      const response = await fetch(request);
      const cache = await caches.open('v1');
      cache.put(request, response.clone());
      return response;
    } catch (error) {
      return await caches.match(request);
    }
  }
};
```

## 4. 跨域问题

### 核心概念
- **同源策略**：协议、域名、端口相同
- **CORS**：跨域资源共享
- **JSONP**：JSON with Padding
- **代理**：服务器代理请求
- **PostMessage**：跨窗口通信

### 常问面试题

#### Q5: 如何解决跨域问题？
**答案：**
```javascript
// 1. CORS - 服务器设置
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*'); // 允许所有域名
  res.header('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
  res.header('Access-Control-Allow-Headers', 'Content-Type, Authorization');
  res.header('Access-Control-Allow-Credentials', 'true');
  
  if (req.method === 'OPTIONS') {
    res.sendStatus(200);
  } else {
    next();
  }
});

// 2. JSONP
function jsonp(url, callback) {
  const script = document.createElement('script');
  const callbackName = 'jsonp_callback_' + Math.round(100000 * Math.random());
  
  window[callbackName] = function(data) {
    callback(data);
    document.body.removeChild(script);
    delete window[callbackName];
  };
  
  script.src = url + '?callback=' + callbackName;
  document.body.appendChild(script);
}

// 使用JSONP
jsonp('http://api.example.com/data', function(data) {
  console.log(data);
});

// 3. 代理服务器
// webpack-dev-server配置
module.exports = {
  devServer: {
    proxy: {
      '/api': {
        target: 'http://localhost:3000',
        changeOrigin: true,
        pathRewrite: {
          '^/api': ''
        }
      }
    }
  }
};

// 4. PostMessage跨窗口通信
// 父窗口
const iframe = document.getElementById('iframe');
iframe.contentWindow.postMessage('Hello from parent', 'http://child-domain.com');

window.addEventListener('message', function(event) {
  if (event.origin !== 'http://child-domain.com') return;
  console.log('Message from child:', event.data);
});

// 子窗口
window.addEventListener('message', function(event) {
  if (event.origin !== 'http://parent-domain.com') return;
  console.log('Message from parent:', event.data);
  
  // 回复消息
  event.source.postMessage('Hello from child', event.origin);
});

// 5. 使用fetch with credentials
fetch('http://api.example.com/data', {
  method: 'POST',
  credentials: 'include', // 包含cookies
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({ data: 'test' })
});
```

## 5. 浏览器安全

### 核心概念
- **XSS攻击**：跨站脚本攻击
- **CSRF攻击**：跨站请求伪造
- **CSP**：内容安全策略
- **HTTPS**：安全传输协议
- **SameSite**：Cookie安全属性

### 常问面试题

#### Q6: 如何防范XSS和CSRF攻击？
**答案：**
```javascript
// 1. XSS防护
// 输入验证和转义
function escapeHtml(text) {
  const div = document.createElement('div');
  div.textContent = text;
  return div.innerHTML;
}

// 使用CSP
app.use((req, res, next) => {
  res.setHeader('Content-Security-Policy', 
    "default-src 'self'; " +
    "script-src 'self' 'unsafe-inline'; " +
    "style-src 'self' 'unsafe-inline'; " +
    "img-src 'self' data: https:;"
  );
  next();
});

// 2. CSRF防护
// 使用CSRF Token
const csrf = require('csurf');
const csrfProtection = csrf({ cookie: true });

app.use(csrfProtection);

app.get('/form', (req, res) => {
  res.render('form', { csrfToken: req.csrfToken() });
});

app.post('/process', csrfProtection, (req, res) => {
  // 处理表单提交
});

// 3. 设置安全的Cookie
app.use(session({
  secret: 'your-secret-key',
  cookie: {
    secure: true, // 只在HTTPS下传输
    httpOnly: true, // 防止XSS
    sameSite: 'strict', // 防止CSRF
    maxAge: 24 * 60 * 60 * 1000 // 1天
  }
}));

// 4. 输入验证
const validator = require('validator');

function validateInput(input) {
  // 验证邮箱
  if (!validator.isEmail(input.email)) {
    throw new Error('Invalid email');
  }
  
  // 验证URL
  if (!validator.isURL(input.website)) {
    throw new Error('Invalid URL');
  }
  
  // 清理HTML
  const cleanHtml = validator.escape(input.html);
  
  return { email: input.email, website: input.website, html: cleanHtml };
}

// 5. 安全的文件上传
const multer = require('multer');
const path = require('path');

const storage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, 'uploads/');
  },
  filename: function (req, file, cb) {
    const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
    cb(null, file.fieldname + '-' + uniqueSuffix + path.extname(file.originalname));
  }
});

const upload = multer({
  storage: storage,
  limits: {
    fileSize: 5 * 1024 * 1024 // 5MB限制
  },
  fileFilter: function (req, file, cb) {
    const allowedTypes = /jpeg|jpg|png|gif/;
    const extname = allowedTypes.test(path.extname(file.originalname).toLowerCase());
    const mimetype = allowedTypes.test(file.mimetype);
    
    if (mimetype && extname) {
      return cb(null, true);
    } else {
      cb(new Error('Only image files are allowed'));
    }
  }
});
```

## 6. 性能优化

### 核心概念
- **资源加载**：优化资源加载顺序
- **代码分割**：按需加载代码
- **图片优化**：图片格式和大小优化
- **缓存策略**：合理使用缓存
- **监控分析**：性能监控和分析

### 常问面试题

#### Q7: 浏览器性能优化策略？
**答案：**
```javascript
// 1. 资源预加载
// HTML中
<link rel="preload" href="/css/critical.css" as="style">
<link rel="preload" href="/js/main.js" as="script">
<link rel="preload" href="/images/hero.jpg" as="image">

// 2. 懒加载
function lazyLoadImages() {
  const images = document.querySelectorAll('img[data-src]');
  const imageObserver = new IntersectionObserver((entries, observer) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        const img = entry.target;
        img.src = img.dataset.src;
        img.classList.remove('lazy');
        observer.unobserve(img);
      }
    });
  });
  
  images.forEach(img => imageObserver.observe(img));
}

// 3. 防抖和节流
function debounce(func, wait) {
  let timeout;
  return function executedFunction(...args) {
    const later = () => {
      clearTimeout(timeout);
      func(...args);
    };
    clearTimeout(timeout);
    timeout = setTimeout(later, wait);
  };
}

function throttle(func, limit) {
  let inThrottle;
  return function() {
    const args = arguments;
    const context = this;
    if (!inThrottle) {
      func.apply(context, args);
      inThrottle = true;
      setTimeout(() => inThrottle = false, limit);
    }
  };
}

// 使用示例
const debouncedSearch = debounce(searchFunction, 300);
const throttledScroll = throttle(scrollFunction, 100);

// 4. 虚拟滚动
class VirtualScroll {
  constructor(container, items, itemHeight) {
    this.container = container;
    this.items = items;
    this.itemHeight = itemHeight;
    this.visibleCount = Math.ceil(container.clientHeight / itemHeight);
    this.startIndex = 0;
    this.endIndex = this.visibleCount;
    
    this.render();
    this.bindEvents();
  }
  
  render() {
    const visibleItems = this.items.slice(this.startIndex, this.endIndex);
    const offsetY = this.startIndex * this.itemHeight;
    
    this.container.innerHTML = '';
    this.container.style.paddingTop = offsetY + 'px';
    
    visibleItems.forEach(item => {
      const element = document.createElement('div');
      element.textContent = item;
      element.style.height = this.itemHeight + 'px';
      this.container.appendChild(element);
    });
  }
  
  bindEvents() {
    this.container.addEventListener('scroll', () => {
      const scrollTop = this.container.scrollTop;
      this.startIndex = Math.floor(scrollTop / this.itemHeight);
      this.endIndex = Math.min(this.startIndex + this.visibleCount, this.items.length);
      this.render();
    });
  }
}

// 5. 性能监控
class PerformanceMonitor {
  constructor() {
    this.metrics = {};
    this.observe();
  }
  
  observe() {
    // 监控页面加载性能
    window.addEventListener('load', () => {
      const navigation = performance.getEntriesByType('navigation')[0];
      this.metrics.loadTime = navigation.loadEventEnd - navigation.loadEventStart;
      this.metrics.domContentLoaded = navigation.domContentLoadedEventEnd - navigation.domContentLoadedEventStart;
      
      console.log('Performance Metrics:', this.metrics);
    });
    
    // 监控资源加载
    const observer = new PerformanceObserver((list) => {
      list.getEntries().forEach(entry => {
        if (entry.entryType === 'resource') {
          console.log('Resource loaded:', entry.name, entry.duration);
        }
      });
    });
    
    observer.observe({ entryTypes: ['resource'] });
  }
}

// 6. 代码分割
// 动态导入
async function loadModule() {
  const module = await import('./heavy-module.js');
  return module.default;
}

// 路由级别的代码分割
const routes = [
  {
    path: '/home',
    component: () => import('./components/Home.vue')
  },
  {
    path: '/about',
    component: () => import('./components/About.vue')
  }
];
```

## 总结

浏览器面试重点：
1. **渲染原理**：渲染流程、DOM树、CSSOM树、重排重绘
2. **事件循环**：调用栈、任务队列、微任务队列、异步执行
3. **缓存机制**：HTTP缓存、强缓存、协商缓存、本地存储
4. **跨域问题**：同源策略、CORS、JSONP、代理、PostMessage
5. **安全防护**：XSS、CSRF、CSP、HTTPS、Cookie安全
6. **性能优化**：资源加载、代码分割、图片优化、缓存策略
7. **现代特性**：Service Worker、Web Workers、WebAssembly
8. **调试工具**：开发者工具、性能分析、网络监控

# HTML 核心概念与面试题

## 1. HTML基础概念

### 核心概念
- **语义化标签**：有意义的HTML标签
- **文档结构**：HTML文档的基本结构
- **标签分类**：块级元素、行内元素、行内块元素
- **属性**：HTML标签的属性
- **表单元素**：输入控件和表单验证

### 常问面试题

#### Q1: HTML5新增了哪些语义化标签？
**答案：**
```html
<!-- HTML5新增的语义化标签 -->
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTML5语义化标签</title>
</head>
<body>
    <!-- 页面头部 -->
    <header>
        <nav>
            <ul>
                <li><a href="#home">首页</a></li>
                <li><a href="#about">关于</a></li>
                <li><a href="#contact">联系</a></li>
            </ul>
        </nav>
    </header>

    <!-- 主要内容区域 -->
    <main>
        <!-- 文章内容 -->
        <article>
            <header>
                <h1>文章标题</h1>
                <time datetime="2024-01-01">2024年1月1日</time>
            </header>
            
            <section>
                <h2>章节标题</h2>
                <p>文章内容...</p>
            </section>
            
            <aside>
                <h3>相关链接</h3>
                <ul>
                    <li><a href="#">相关文章1</a></li>
                    <li><a href="#">相关文章2</a></li>
                </ul>
            </aside>
        </article>
    </main>

    <!-- 侧边栏 -->
    <aside>
        <h3>侧边栏内容</h3>
        <p>侧边栏信息...</p>
    </aside>

    <!-- 页面底部 -->
    <footer>
        <p>&copy; 2024 网站名称. 保留所有权利.</p>
    </footer>
</body>
</html>

<!-- 新增的语义化标签：
- header: 页面或区域的头部
- nav: 导航链接
- main: 主要内容区域
- article: 独立的文章内容
- section: 文档中的节
- aside: 侧边栏内容
- footer: 页面或区域的底部
- time: 时间信息
- figure: 图片、图表等媒体内容
- figcaption: 媒体内容的标题
-->
```

#### Q2: HTML表单元素和验证？
**答案：**
```html
<!-- HTML5表单元素和验证 -->
<form action="/submit" method="POST" novalidate>
    <!-- 文本输入 -->
    <div>
        <label for="username">用户名：</label>
        <input type="text" id="username" name="username" 
               required minlength="3" maxlength="20" 
               pattern="[a-zA-Z0-9_]+"
               placeholder="请输入用户名">
    </div>

    <!-- 邮箱输入 -->
    <div>
        <label for="email">邮箱：</label>
        <input type="email" id="email" name="email" 
               required placeholder="请输入邮箱">
    </div>

    <!-- 密码输入 -->
    <div>
        <label for="password">密码：</label>
        <input type="password" id="password" name="password" 
               required minlength="6" placeholder="请输入密码">
    </div>

    <!-- 数字输入 -->
    <div>
        <label for="age">年龄：</label>
        <input type="number" id="age" name="age" 
               min="1" max="120" placeholder="请输入年龄">
    </div>

    <!-- 日期输入 -->
    <div>
        <label for="birthday">生日：</label>
        <input type="date" id="birthday" name="birthday">
    </div>

    <!-- 时间输入 -->
    <div>
        <label for="time">时间：</label>
        <input type="time" id="time" name="time">
    </div>

    <!-- 颜色选择 -->
    <div>
        <label for="color">颜色：</label>
        <input type="color" id="color" name="color">
    </div>

    <!-- 范围滑块 -->
    <div>
        <label for="range">范围：</label>
        <input type="range" id="range" name="range" 
               min="0" max="100" value="50">
    </div>

    <!-- 文件上传 -->
    <div>
        <label for="file">文件：</label>
        <input type="file" id="file" name="file" 
               accept=".jpg,.png,.pdf">
    </div>

    <!-- 选择框 -->
    <div>
        <label for="country">国家：</label>
        <select id="country" name="country" required>
            <option value="">请选择国家</option>
            <option value="china">中国</option>
            <option value="usa">美国</option>
            <option value="japan">日本</option>
        </select>
    </div>

    <!-- 多选框 -->
    <div>
        <label>爱好：</label>
        <input type="checkbox" id="reading" name="hobbies" value="reading">
        <label for="reading">阅读</label>
        <input type="checkbox" id="music" name="hobbies" value="music">
        <label for="music">音乐</label>
        <input type="checkbox" id="sports" name="hobbies" value="sports">
        <label for="sports">运动</label>
    </div>

    <!-- 单选框 -->
    <div>
        <label>性别：</label>
        <input type="radio" id="male" name="gender" value="male">
        <label for="male">男</label>
        <input type="radio" id="female" name="gender" value="female">
        <label for="female">女</label>
    </div>

    <!-- 文本域 -->
    <div>
        <label for="message">留言：</label>
        <textarea id="message" name="message" 
                  rows="4" cols="50" 
                  placeholder="请输入留言"></textarea>
    </div>

    <!-- 提交按钮 -->
    <div>
        <button type="submit">提交</button>
        <button type="reset">重置</button>
    </div>
</form>

<!-- 表单验证属性：
- required: 必填字段
- minlength/maxlength: 最小/最大长度
- min/max: 数值范围
- pattern: 正则表达式验证
- novalidate: 禁用浏览器默认验证
-->
```

## 2. HTML语义化

### 核心概念
- **语义化标签**：有意义的HTML标签
- **无障碍访问**：屏幕阅读器友好
- **SEO优化**：搜索引擎优化
- **代码可读性**：代码结构清晰
- **维护性**：便于维护和修改

### 常问面试题

#### Q3: 为什么要使用语义化标签？
**答案：**
```html
<!-- 非语义化写法 -->
<div class="header">
    <div class="nav">
        <div class="nav-item"><a href="#home">首页</a></div>
        <div class="nav-item"><a href="#about">关于</a></div>
    </div>
</div>

<div class="main">
    <div class="article">
        <div class="title">文章标题</div>
        <div class="content">文章内容...</div>
    </div>
</div>

<div class="footer">
    <div class="copyright">版权信息</div>
</div>

<!-- 语义化写法 -->
<header>
    <nav>
        <ul>
            <li><a href="#home">首页</a></li>
            <li><a href="#about">关于</a></li>
        </ul>
    </nav>
</header>

<main>
    <article>
        <h1>文章标题</h1>
        <p>文章内容...</p>
    </article>
</main>

<footer>
    <p>&copy; 2024 版权信息</p>
</footer>

<!-- 语义化标签的优势：
1. 提高可访问性：屏幕阅读器能更好地理解页面结构
2. SEO友好：搜索引擎能更好地理解页面内容
3. 代码可读性：开发者能快速理解页面结构
4. 维护性：便于后续维护和修改
5. 样式分离：结构与表现分离
-->
```

## 3. HTML5新特性

### 核心概念
- **Canvas**：2D图形绘制
- **SVG**：矢量图形
- **Web Storage**：本地存储
- **Web Workers**：后台线程
- **Geolocation**：地理位置
- **WebSocket**：实时通信

### 常问面试题

#### Q4: HTML5的Canvas和SVG有什么区别？
**答案：**
```html
<!-- Canvas - 位图，适合游戏和图像处理 -->
<canvas id="myCanvas" width="400" height="200"></canvas>

<script>
const canvas = document.getElementById('myCanvas');
const ctx = canvas.getContext('2d');

// 绘制矩形
ctx.fillStyle = 'red';
ctx.fillRect(10, 10, 100, 100);

// 绘制圆形
ctx.beginPath();
ctx.arc(200, 60, 50, 0, 2 * Math.PI);
ctx.fillStyle = 'blue';
ctx.fill();

// 绘制文字
ctx.font = '20px Arial';
ctx.fillStyle = 'black';
ctx.fillText('Hello Canvas', 10, 150);
</script>

<!-- SVG - 矢量图，适合图标和简单图形 -->
<svg width="400" height="200">
    <!-- 矩形 -->
    <rect x="10" y="10" width="100" height="100" fill="red" />
    
    <!-- 圆形 -->
    <circle cx="200" cy="60" r="50" fill="blue" />
    
    <!-- 文字 -->
    <text x="10" y="150" font-family="Arial" font-size="20" fill="black">
        Hello SVG
    </text>
    
    <!-- 路径 -->
    <path d="M 50 100 L 100 50 L 150 100 Z" fill="green" />
</svg>

<!-- Canvas vs SVG 对比：
Canvas:
- 位图，像素操作
- 适合游戏、图像处理
- 性能好，适合大量元素
- 不支持事件处理
- 需要JavaScript操作

SVG:
- 矢量图，数学描述
- 适合图标、简单图形
- 支持事件处理
- 可缩放不失真
- 可直接在HTML中使用
- 支持CSS样式
-->
```

## 4. HTML性能优化

### 核心概念
- **资源加载**：优化资源加载顺序
- **图片优化**：图片格式和大小优化
- **代码压缩**：HTML代码压缩
- **缓存策略**：浏览器缓存
- **CDN加速**：内容分发网络

### 常问面试题

#### Q5: HTML性能优化技巧？
**答案：**
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    
    <!-- 1. 预加载关键资源 -->
    <link rel="preload" href="/css/critical.css" as="style">
    <link rel="preload" href="/js/main.js" as="script">
    <link rel="preload" href="/images/hero.jpg" as="image">
    
    <!-- 2. DNS预解析 -->
    <link rel="dns-prefetch" href="//fonts.googleapis.com">
    <link rel="dns-prefetch" href="//cdn.example.com">
    
    <!-- 3. 关键CSS内联 -->
    <style>
        /* 关键CSS内联到HTML中 */
        body { margin: 0; font-family: Arial, sans-serif; }
        .hero { background: #f0f0f0; padding: 2rem; }
    </style>
    
    <!-- 4. 非关键CSS异步加载 -->
    <link rel="preload" href="/css/non-critical.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
    <noscript><link rel="stylesheet" href="/css/non-critical.css"></noscript>
    
    <title>性能优化示例</title>
</head>
<body>
    <!-- 5. 图片优化 -->
    <!-- 使用现代图片格式 -->
    <picture>
        <source srcset="image.webp" type="image/webp">
        <source srcset="image.avif" type="image/avif">
        <img src="image.jpg" alt="描述" loading="lazy" width="800" height="600">
    </picture>
    
    <!-- 6. 懒加载 -->
    <img src="placeholder.jpg" data-src="real-image.jpg" alt="懒加载图片" loading="lazy">
    
    <!-- 7. 资源提示 -->
    <link rel="prefetch" href="/next-page.html">
    <link rel="preconnect" href="https://api.example.com">
    
    <!-- 8. 关键内容优先 -->
    <main>
        <h1>主要内容</h1>
        <p>这是页面的主要内容，应该优先加载。</p>
    </main>
    
    <!-- 9. 非关键内容后加载 -->
    <aside>
        <h2>侧边栏内容</h2>
        <p>这是侧边栏内容，可以后加载。</p>
    </aside>
    
    <!-- 10. 脚本优化 -->
    <!-- 关键脚本内联 -->
    <script>
        // 关键JavaScript代码
        document.addEventListener('DOMContentLoaded', function() {
            // 初始化代码
        });
    </script>
    
    <!-- 非关键脚本异步加载 -->
    <script src="/js/analytics.js" async></script>
    <script src="/js/main.js" defer></script>
</body>
</html>

<!-- 性能优化技巧总结：
1. 预加载关键资源
2. DNS预解析
3. 关键CSS内联
4. 非关键CSS异步加载
5. 图片优化和懒加载
6. 资源提示
7. 关键内容优先
8. 脚本优化
9. 代码压缩
10. 使用CDN
-->
```

## 5. HTML无障碍访问

### 核心概念
- **ARIA属性**：无障碍访问属性
- **语义化标签**：有意义的HTML标签
- **键盘导航**：键盘操作支持
- **屏幕阅读器**：辅助技术支持
- **颜色对比度**：视觉可访问性

### 常问面试题

#### Q6: 如何实现HTML无障碍访问？
**答案：**
```html
<!-- 无障碍访问示例 -->
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>无障碍访问示例</title>
</head>
<body>
    <!-- 1. 使用语义化标签 -->
    <header role="banner">
        <nav role="navigation" aria-label="主导航">
            <ul>
                <li><a href="#home" aria-current="page">首页</a></li>
                <li><a href="#about">关于我们</a></li>
                <li><a href="#contact">联系我们</a></li>
            </ul>
        </nav>
    </header>

    <!-- 2. 表单无障碍 -->
    <main role="main">
        <form aria-label="用户注册表单">
            <fieldset>
                <legend>用户信息</legend>
                
                <div>
                    <label for="username">用户名 <span aria-label="必填">*</span></label>
                    <input type="text" id="username" name="username" 
                           required aria-describedby="username-help"
                           aria-invalid="false">
                    <div id="username-help" class="help-text">
                        用户名必须是3-20个字符，只能包含字母、数字和下划线
                    </div>
                </div>

                <div>
                    <label for="email">邮箱地址 <span aria-label="必填">*</span></label>
                    <input type="email" id="email" name="email" 
                           required aria-describedby="email-help">
                    <div id="email-help" class="help-text">
                        请输入有效的邮箱地址
                    </div>
                </div>

                <div>
                    <label for="password">密码 <span aria-label="必填">*</span></label>
                    <input type="password" id="password" name="password" 
                           required aria-describedby="password-help">
                    <div id="password-help" class="help-text">
                        密码至少6个字符
                    </div>
                </div>
            </fieldset>

            <button type="submit" aria-describedby="submit-help">注册</button>
            <div id="submit-help" class="help-text">
                点击注册按钮创建新账户
            </div>
        </form>
    </main>

    <!-- 3. 图片无障碍 -->
    <section>
        <h2>产品展示</h2>
        <img src="product.jpg" alt="智能手机产品图片，黑色机身，6.1英寸屏幕" 
             width="300" height="200">
        
        <!-- 装饰性图片 -->
        <img src="decoration.jpg" alt="" role="presentation">
    </section>

    <!-- 4. 表格无障碍 -->
    <section>
        <h2>价格表</h2>
        <table>
            <caption>产品价格对比表</caption>
            <thead>
                <tr>
                    <th scope="col">产品名称</th>
                    <th scope="col">价格</th>
                    <th scope="col">库存</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <th scope="row">智能手机</th>
                    <td>¥2999</td>
                    <td>50</td>
                </tr>
                <tr>
                    <th scope="row">平板电脑</th>
                    <td>¥1999</td>
                    <td>30</td>
                </tr>
            </tbody>
        </table>
    </section>

    <!-- 5. 动态内容无障碍 -->
    <section>
        <h2>通知</h2>
        <div id="notification" role="alert" aria-live="polite" aria-atomic="true">
            <!-- 动态通知内容 -->
        </div>
        
        <button onclick="showNotification()" aria-describedby="notification">
            显示通知
        </button>
    </section>

    <!-- 6. 键盘导航 -->
    <section>
        <h2>操作按钮</h2>
        <div role="toolbar" aria-label="编辑工具栏">
            <button type="button" aria-label="粗体" tabindex="0">B</button>
            <button type="button" aria-label="斜体" tabindex="0">I</button>
            <button type="button" aria-label="下划线" tabindex="0">U</button>
        </div>
    </section>

    <!-- 7. 跳过链接 -->
    <a href="#main-content" class="skip-link">跳过导航到主要内容</a>

    <script>
        // 动态内容更新
        function showNotification() {
            const notification = document.getElementById('notification');
            notification.textContent = '这是一条重要通知！';
            
            // 确保屏幕阅读器能读取到更新
            notification.setAttribute('aria-live', 'assertive');
        }

        // 表单验证反馈
        document.getElementById('username').addEventListener('blur', function() {
            const value = this.value;
            const isValid = value.length >= 3 && value.length <= 20;
            
            this.setAttribute('aria-invalid', !isValid);
            
            if (!isValid) {
                this.setAttribute('aria-describedby', 'username-error');
            }
        });
    </script>
</body>
</html>

<!-- 无障碍访问要点：
1. 使用语义化标签
2. 提供有意义的alt属性
3. 使用ARIA属性
4. 支持键盘导航
5. 提供足够的颜色对比度
6. 使用标题结构
7. 提供表单标签和帮助文本
8. 支持屏幕阅读器
-->
```

## 总结

HTML面试重点：
1. **基础概念**：语义化标签、文档结构、标签分类
2. **HTML5新特性**：新增标签、表单元素、Canvas、SVG
3. **语义化**：语义化标签、无障碍访问、SEO优化
4. **表单处理**：表单元素、验证、无障碍访问
5. **性能优化**：资源加载、图片优化、代码压缩
6. **无障碍访问**：ARIA属性、键盘导航、屏幕阅读器支持
7. **现代特性**：Web Storage、Web Workers、Geolocation
8. **最佳实践**：代码规范、性能优化、可维护性

# CSS 核心概念与面试题

## 1. CSS基础概念

### 核心概念
- **选择器**：选择HTML元素的方式
- **盒模型**：元素的内容、内边距、边框、外边距
- **定位**：static、relative、absolute、fixed、sticky
- **布局**：flexbox、grid、float、position
- **响应式设计**：媒体查询、移动端适配

### 常问面试题

#### Q1: CSS盒模型是什么？
**答案：**
```css
/* 标准盒模型 */
.box {
  width: 200px;
  height: 100px;
  padding: 20px;
  border: 5px solid #000;
  margin: 10px;
  box-sizing: content-box; /* 默认值 */
}

/* IE盒模型 */
.box-ie {
  width: 200px;
  height: 100px;
  padding: 20px;
  border: 5px solid #000;
  margin: 10px;
  box-sizing: border-box;
}

/* 盒模型说明：
- content-box: width/height = 内容区域
- border-box: width/height = 内容 + padding + border
- margin 不包含在盒模型内
*/
```

#### Q2: CSS选择器优先级？
**答案：**
```css
/* 选择器优先级计算：
内联样式(1000) > ID选择器(100) > 类选择器(10) > 元素选择器(1) > 通配符(0)
*/

/* 通配符选择器 - 优先级: 0 */
* { margin: 0; }

/* 元素选择器 - 优先级: 1 */
div { color: red; }

/* 类选择器 - 优先级: 10 */
.container { color: blue; }

/* ID选择器 - 优先级: 100 */
#header { color: green; }

/* 内联样式 - 优先级: 1000 */
/* <div style="color: purple;"> */

/* 组合选择器优先级相加 */
div.container { color: orange; } /* 1 + 10 = 11 */
#header .title { color: pink; } /* 100 + 10 = 110 */

/* !important 最高优先级 */
.important { color: red !important; }
```

## 2. CSS布局

### 核心概念
- **Flexbox**：一维布局
- **Grid**：二维布局
- **Float**：传统布局方式
- **Position**：定位布局
- **响应式布局**：适配不同屏幕

### 常问面试题

#### Q3: Flexbox布局详解？
**答案：**
```css
/* Flexbox容器属性 */
.flex-container {
  display: flex;
  flex-direction: row; /* row | row-reverse | column | column-reverse */
  flex-wrap: nowrap; /* nowrap | wrap | wrap-reverse */
  justify-content: center; /* flex-start | flex-end | center | space-between | space-around | space-evenly */
  align-items: center; /* flex-start | flex-end | center | baseline | stretch */
  align-content: center; /* flex-start | flex-end | center | space-between | space-around | stretch */
  gap: 20px; /* 项目间距 */
}

/* Flexbox项目属性 */
.flex-item {
  flex-grow: 1; /* 放大比例 */
  flex-shrink: 1; /* 缩小比例 */
  flex-basis: auto; /* 基础大小 */
  flex: 1 1 auto; /* 简写: grow shrink basis */
  align-self: center; /* 单独对齐 */
  order: 1; /* 排序 */
}

/* 实际应用示例 */
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem;
}

.card-container {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}

.card {
  flex: 1 1 300px; /* 最小300px，可伸缩 */
  min-width: 0; /* 防止内容溢出 */
}
```

#### Q4: CSS Grid布局？
**答案：**
```css
/* Grid容器属性 */
.grid-container {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr; /* 列定义 */
  grid-template-rows: auto 1fr auto; /* 行定义 */
  grid-template-areas: 
    "header header header"
    "sidebar main aside"
    "footer footer footer";
  gap: 20px; /* 网格间距 */
  justify-items: center; /* 水平对齐 */
  align-items: center; /* 垂直对齐 */
}

/* Grid项目属性 */
.grid-item {
  grid-column: 1 / 3; /* 跨越列 */
  grid-row: 1 / 2; /* 跨越行 */
  grid-area: header; /* 网格区域 */
  justify-self: start; /* 单独水平对齐 */
  align-self: end; /* 单独垂直对齐 */
}

/* 响应式Grid */
.responsive-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1rem;
}

/* 复杂布局示例 */
.layout {
  display: grid;
  grid-template-columns: 200px 1fr 200px;
  grid-template-rows: 60px 1fr 60px;
  grid-template-areas: 
    "header header header"
    "sidebar main aside"
    "footer footer footer";
  height: 100vh;
}

.header { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main { grid-area: main; }
.aside { grid-area: aside; }
.footer { grid-area: footer; }
```

## 3. CSS动画与过渡

### 核心概念
- **Transition**：过渡动画
- **Animation**：关键帧动画
- **Transform**：变换效果
- **性能优化**：GPU加速
- **动画库**：第三方动画库

### 常问面试题

#### Q5: CSS动画实现？
**答案：**
```css
/* Transition过渡 */
.button {
  background: blue;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  transition: all 0.3s ease; /* 属性 持续时间 缓动函数 */
}

.button:hover {
  background: red;
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
}

/* Animation关键帧动画 */
@keyframes slideIn {
  0% {
    transform: translateX(-100%);
    opacity: 0;
  }
  50% {
    opacity: 0.5;
  }
  100% {
    transform: translateX(0);
    opacity: 1;
  }
}

.slide-in {
  animation: slideIn 1s ease-in-out;
  animation-fill-mode: both; /* 保持动画结束状态 */
}

/* 复杂动画示例 */
@keyframes bounce {
  0%, 20%, 50%, 80%, 100% {
    transform: translateY(0);
  }
  40% {
    transform: translateY(-30px);
  }
  60% {
    transform: translateY(-15px);
  }
}

.bounce {
  animation: bounce 2s infinite;
}

/* 性能优化 */
.optimized-animation {
  transform: translateZ(0); /* 开启硬件加速 */
  will-change: transform; /* 提示浏览器优化 */
  backface-visibility: hidden; /* 隐藏背面 */
}
```

## 4. CSS预处理器

### 核心概念
- **Sass/SCSS**：功能强大的预处理器
- **Less**：JavaScript实现的预处理器
- **变量**：可复用的值
- **嵌套**：选择器嵌套
- **混入**：可复用的样式块

### 常问面试题

#### Q6: Sass/SCSS使用？
**答案：**
```scss
// 变量
$primary-color: #007bff;
$secondary-color: #6c757d;
$font-size-base: 16px;
$border-radius: 4px;

// 嵌套
.navbar {
  background: $primary-color;
  padding: 1rem;
  
  .nav-item {
    color: white;
    text-decoration: none;
    
    &:hover {
      color: lighten($primary-color, 20%);
    }
    
    &.active {
      font-weight: bold;
    }
  }
}

// 混入
@mixin button-style($bg-color, $text-color: white) {
  background: $bg-color;
  color: $text-color;
  padding: 0.5rem 1rem;
  border: none;
  border-radius: $border-radius;
  cursor: pointer;
  
  &:hover {
    background: darken($bg-color, 10%);
  }
}

.btn-primary {
  @include button-style($primary-color);
}

.btn-secondary {
  @include button-style($secondary-color);
}

// 继承
%base-button {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: $border-radius;
  cursor: pointer;
}

.btn-success {
  @extend %base-button;
  background: green;
  color: white;
}

// 函数
@function rem($pixels) {
  @return $pixels / $font-size-base * 1rem;
}

.text-large {
  font-size: rem(24px);
}

// 条件语句
@mixin theme($theme: light) {
  @if $theme == light {
    background: white;
    color: black;
  } @else if $theme == dark {
    background: black;
    color: white;
  }
}

.light-theme {
  @include theme(light);
}

.dark-theme {
  @include theme(dark);
}
```

## 5. CSS性能优化

### 核心概念
- **选择器优化**：高效的选择器
- **重排重绘**：减少布局和绘制
- **GPU加速**：硬件加速
- **代码分割**：按需加载
- **压缩优化**：文件大小优化

### 常问面试题

#### Q7: CSS性能优化技巧？
**答案：**
```css
/* 1. 选择器优化 */
/* 避免复杂选择器 */
.bad-selector div ul li a { color: red; } /* 性能差 */
.good-selector .link { color: red; } /* 性能好 */

/* 2. 避免重排重绘 */
.optimized {
  /* 使用transform代替改变位置 */
  transform: translateX(100px); /* 只触发合成层 */
  /* 而不是 */
  /* left: 100px; */ /* 触发重排 */
}

/* 3. 使用will-change提示浏览器 */
.animation-element {
  will-change: transform, opacity;
}

/* 4. 避免频繁改变样式 */
.dynamic-element {
  /* 批量改变样式 */
  transform: translateX(100px) scale(1.2);
  opacity: 0.8;
}

/* 5. 使用contain属性 */
.isolated-element {
  contain: layout style paint;
}

/* 6. 优化动画性能 */
.smooth-animation {
  transform: translateZ(0); /* 开启硬件加速 */
  backface-visibility: hidden;
  perspective: 1000px;
}

/* 7. 使用CSS变量减少重复 */
:root {
  --primary-color: #007bff;
  --secondary-color: #6c757d;
  --border-radius: 4px;
}

.button {
  background: var(--primary-color);
  border-radius: var(--border-radius);
}

/* 8. 媒体查询优化 */
@media (max-width: 768px) {
  .mobile-only {
    display: block;
  }
}

/* 9. 使用CSS Grid和Flexbox */
.modern-layout {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1rem;
}

/* 10. 避免@import */
/* 使用link标签代替@import */
<link rel="stylesheet" href="styles.css">
```

## 总结

CSS面试重点：
1. **基础概念**：选择器、盒模型、定位、布局
2. **布局技术**：Flexbox、Grid、Float、Position
3. **动画效果**：Transition、Animation、Transform
4. **预处理器**：Sass/SCSS、Less、变量、混入
5. **性能优化**：选择器优化、重排重绘、GPU加速
6. **响应式设计**：媒体查询、移动端适配
7. **现代特性**：CSS变量、容器查询、逻辑属性
8. **最佳实践**：代码规范、性能优化、可维护性

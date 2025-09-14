# JavaScript 核心概念与面试题

## 1. ES6+ 新特性

### 核心概念
- **箭头函数**：语法简洁，不绑定this
- **解构赋值**：数组和对象解构
- **模板字符串**：反引号和插值表达式
- **Promise/async-await**：异步编程解决方案
- **模块化**：import/export
- **类语法**：class关键字
- **Symbol**：唯一标识符
- **Set/Map**：新的数据结构

### 常问面试题

#### Q1: 箭头函数和普通函数的区别？
**答案：**
```javascript
// 1. this绑定不同
const obj = {
  name: 'test',
  normalFunc: function() {
    console.log(this.name); // 'test'
  },
  arrowFunc: () => {
    console.log(this.name); // undefined (指向全局)
  }
};

// 2. 不能作为构造函数
const Arrow = () => {};
new Arrow(); // TypeError: Arrow is not a constructor

// 3. 没有arguments对象
function normal() {
  console.log(arguments); // 有arguments
}
const arrow = () => {
  console.log(arguments); // ReferenceError
};
```

#### Q2: Promise的三种状态和链式调用？
**答案：**
```javascript
// 三种状态：pending, fulfilled, rejected
const promise = new Promise((resolve, reject) => {
  // 异步操作
  setTimeout(() => {
    resolve('success');
  }, 1000);
});

// 链式调用
promise
  .then(result => {
    console.log(result); // 'success'
    return result.toUpperCase();
  })
  .then(upperResult => {
    console.log(upperResult); // 'SUCCESS'
  })
  .catch(error => {
    console.error(error);
  });
```

#### Q3: async/await和Promise的区别？
**答案：**
```javascript
// Promise方式
function fetchData() {
  return fetch('/api/data')
    .then(response => response.json())
    .then(data => {
      console.log(data);
      return data;
    })
    .catch(error => {
      console.error(error);
    });
}

// async/await方式
async function fetchData() {
  try {
    const response = await fetch('/api/data');
    const data = await response.json();
    console.log(data);
    return data;
  } catch (error) {
    console.error(error);
  }
}
```

## 2. 闭包与作用域

### 核心概念
- **词法作用域**：函数定义时确定作用域
- **闭包**：函数能够访问外部作用域的变量
- **变量提升**：var声明提升，let/const暂时性死区
- **this绑定**：动态绑定规则

### 常问面试题

#### Q4: 什么是闭包？请举例说明
**答案：**
```javascript
// 闭包示例1：计数器
function createCounter() {
  let count = 0;
  return function() {
    count++;
    return count;
  };
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2

// 闭包示例2：模块模式
const module = (function() {
  let privateVar = 0;
  
  return {
    increment: function() {
      privateVar++;
    },
    getValue: function() {
      return privateVar;
    }
  };
})();
```

#### Q5: 经典闭包面试题
**答案：**
```javascript
// 问题：输出什么？
for (var i = 0; i < 3; i++) {
  setTimeout(() => {
    console.log(i); // 输出：3, 3, 3
  }, 100);
}

// 解决方案1：使用let
for (let i = 0; i < 3; i++) {
  setTimeout(() => {
    console.log(i); // 输出：0, 1, 2
  }, 100);
}

// 解决方案2：使用闭包
for (var i = 0; i < 3; i++) {
  (function(j) {
    setTimeout(() => {
      console.log(j); // 输出：0, 1, 2
    }, 100);
  })(i);
}
```

## 3. 原型与继承

### 核心概念
- **原型链**：对象通过__proto__查找属性
- **构造函数**：用于创建对象的函数
- **prototype**：构造函数的原型对象
- **继承**：原型继承、构造函数继承、组合继承

### 常问面试题

#### Q6: 原型链是什么？如何实现继承？
**答案：**
```javascript
// 原型链示例
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function() {
  console.log(`Hello, I'm ${this.name}`);
};

function Student(name, grade) {
  Person.call(this, name); // 构造函数继承
  this.grade = grade;
}

// 原型继承
Student.prototype = Object.create(Person.prototype);
Student.prototype.constructor = Student;

Student.prototype.study = function() {
  console.log(`${this.name} is studying`);
};

const student = new Student('Alice', 'A');
student.sayHello(); // Hello, I'm Alice
student.study(); // Alice is studying
```

#### Q7: ES6 Class继承
**答案：**
```javascript
class Person {
  constructor(name) {
    this.name = name;
  }
  
  sayHello() {
    console.log(`Hello, I'm ${this.name}`);
  }
}

class Student extends Person {
  constructor(name, grade) {
    super(name); // 调用父类构造函数
    this.grade = grade;
  }
  
  study() {
    console.log(`${this.name} is studying`);
  }
}

const student = new Student('Bob', 'B');
student.sayHello(); // Hello, I'm Bob
student.study(); // Bob is studying
```

## 4. 异步编程

### 核心概念
- **事件循环**：调用栈、任务队列、微任务队列
- **宏任务**：setTimeout、setInterval、I/O
- **微任务**：Promise.then、queueMicrotask
- **并发模型**：单线程非阻塞

### 常问面试题

#### Q8: 事件循环执行顺序
**答案：**
```javascript
console.log('1');

setTimeout(() => {
  console.log('2');
}, 0);

Promise.resolve().then(() => {
  console.log('3');
});

console.log('4');

// 输出顺序：1, 4, 3, 2
// 解释：同步代码 -> 微任务 -> 宏任务
```

#### Q9: 实现Promise.all
**答案：**
```javascript
function promiseAll(promises) {
  return new Promise((resolve, reject) => {
    if (!Array.isArray(promises)) {
      reject(new TypeError('参数必须是数组'));
      return;
    }
    
    const results = [];
    let completedCount = 0;
    
    if (promises.length === 0) {
      resolve(results);
      return;
    }
    
    promises.forEach((promise, index) => {
      Promise.resolve(promise)
        .then(value => {
          results[index] = value;
          completedCount++;
          
          if (completedCount === promises.length) {
            resolve(results);
          }
        })
        .catch(reject);
    });
  });
}

// 使用示例
const promises = [
  Promise.resolve(1),
  Promise.resolve(2),
  Promise.resolve(3)
];

promiseAll(promises).then(results => {
  console.log(results); // [1, 2, 3]
});
```

## 5. 函数式编程

### 核心概念
- **纯函数**：相同输入总是产生相同输出
- **不可变性**：不修改原始数据
- **高阶函数**：接受或返回函数的函数
- **柯里化**：将多参数函数转换为单参数函数链

### 常问面试题

#### Q10: 实现数组的map、filter、reduce
**答案：**
```javascript
// 实现map
Array.prototype.myMap = function(callback) {
  const result = [];
  for (let i = 0; i < this.length; i++) {
    result.push(callback(this[i], i, this));
  }
  return result;
};

// 实现filter
Array.prototype.myFilter = function(callback) {
  const result = [];
  for (let i = 0; i < this.length; i++) {
    if (callback(this[i], i, this)) {
      result.push(this[i]);
    }
  }
  return result;
};

// 实现reduce
Array.prototype.myReduce = function(callback, initialValue) {
  let accumulator = initialValue !== undefined ? initialValue : this[0];
  let startIndex = initialValue !== undefined ? 0 : 1;
  
  for (let i = startIndex; i < this.length; i++) {
    accumulator = callback(accumulator, this[i], i, this);
  }
  
  return accumulator;
};

// 使用示例
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.myMap(x => x * 2);
const evens = numbers.myFilter(x => x % 2 === 0);
const sum = numbers.myReduce((acc, curr) => acc + curr, 0);
```

## 6. 性能优化

### 核心概念
- **防抖节流**：控制函数执行频率
- **懒加载**：延迟加载资源
- **虚拟滚动**：处理大量数据渲染
- **内存管理**：避免内存泄漏

### 常问面试题

#### Q11: 实现防抖和节流
**答案：**
```javascript
// 防抖：延迟执行，如果在延迟期间再次触发则重新计时
function debounce(func, delay) {
  let timeoutId;
  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

// 节流：限制执行频率，在指定时间内只执行一次
function throttle(func, delay) {
  let lastExecTime = 0;
  return function(...args) {
    const currentTime = Date.now();
    if (currentTime - lastExecTime >= delay) {
      func.apply(this, args);
      lastExecTime = currentTime;
    }
  };
}

// 使用示例
const debouncedSearch = debounce((query) => {
  console.log('搜索:', query);
}, 300);

const throttledScroll = throttle(() => {
  console.log('滚动事件');
}, 100);
```

## 7. 现代JavaScript特性

### 核心概念
- **可选链操作符**：?. 安全访问属性
- **空值合并操作符**：?? 提供默认值
- **BigInt**：大整数类型
- **动态导入**：import() 动态加载模块

### 常问面试题

#### Q12: 可选链和空值合并操作符
**答案：**
```javascript
// 可选链操作符
const user = {
  profile: {
    name: 'John',
    address: {
      city: 'New York'
    }
  }
};

// 传统写法
const city = user && user.profile && user.profile.address && user.profile.address.city;

// 可选链写法
const city = user?.profile?.address?.city;

// 空值合并操作符
const name = user?.name ?? 'Anonymous';
const age = user?.age ?? 0;

// 组合使用
const displayName = user?.profile?.name ?? 'Guest';
```

## 8. 模块化

### 核心概念
- **CommonJS**：require/module.exports
- **ES Modules**：import/export
- **模块加载**：同步vs异步
- **循环依赖**：模块间的相互引用

### 常问面试题

#### Q13: CommonJS和ES Modules的区别
**答案：**
```javascript
// CommonJS (Node.js)
// math.js
function add(a, b) {
  return a + b;
}
module.exports = { add };

// main.js
const { add } = require('./math');
console.log(add(1, 2));

// ES Modules
// math.js
export function add(a, b) {
  return a + b;
}

// main.js
import { add } from './math.js';
console.log(add(1, 2));

// 主要区别：
// 1. 加载时机：CommonJS运行时加载，ES Modules编译时加载
// 2. 输出：CommonJS输出值的拷贝，ES Modules输出值的引用
// 3. 同步性：CommonJS同步加载，ES Modules异步加载
```

## 9. 错误处理

### 核心概念
- **try-catch**：同步错误捕获
- **Promise错误处理**：catch方法
- **全局错误处理**：window.onerror
- **错误边界**：React中的错误处理

### 常问面试题

#### Q14: 错误处理最佳实践
**答案：**
```javascript
// 同步错误处理
try {
  const result = JSON.parse('invalid json');
} catch (error) {
  console.error('解析JSON失败:', error.message);
}

// Promise错误处理
fetch('/api/data')
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return response.json();
  })
  .catch(error => {
    console.error('请求失败:', error.message);
  });

// async/await错误处理
async function fetchData() {
  try {
    const response = await fetch('/api/data');
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return await response.json();
  } catch (error) {
    console.error('请求失败:', error.message);
    throw error; // 重新抛出错误
  }
}

// 全局错误处理
window.addEventListener('error', (event) => {
  console.error('全局错误:', event.error);
});

window.addEventListener('unhandledrejection', (event) => {
  console.error('未处理的Promise拒绝:', event.reason);
});
```

## 10. 性能监控与调试

### 核心概念
- **性能API**：Performance API
- **内存泄漏检测**：Chrome DevTools
- **代码分析**：Profiler工具
- **Bundle分析**：webpack-bundle-analyzer

### 常问面试题

#### Q15: 如何检测和解决内存泄漏？
**答案：**
```javascript
// 内存泄漏示例
function createLeak() {
  const largeArray = new Array(1000000).fill('data');
  
  // 错误：没有清理定时器
  setInterval(() => {
    console.log('定时器运行中...');
  }, 1000);
  
  // 错误：没有移除事件监听器
  document.addEventListener('click', () => {
    console.log('点击事件');
  });
}

// 正确的做法
function createNoLeak() {
  const largeArray = new Array(1000000).fill('data');
  
  // 正确：保存定时器ID并清理
  const timerId = setInterval(() => {
    console.log('定时器运行中...');
  }, 1000);
  
  // 正确：保存事件处理函数引用
  const handleClick = () => {
    console.log('点击事件');
  };
  
  document.addEventListener('click', handleClick);
  
  // 提供清理方法
  return {
    cleanup: () => {
      clearInterval(timerId);
      document.removeEventListener('click', handleClick);
      largeArray.length = 0; // 清空数组
    }
  };
}

// 使用Performance API监控
function measurePerformance() {
  const start = performance.now();
  
  // 执行一些操作
  for (let i = 0; i < 1000000; i++) {
    Math.random();
  }
  
  const end = performance.now();
  console.log(`操作耗时: ${end - start} 毫秒`);
}
```

## 总结

JavaScript面试重点：
1. **ES6+新特性**：箭头函数、Promise、async/await、解构赋值
2. **闭包与作用域**：词法作用域、this绑定、变量提升
3. **原型与继承**：原型链、继承实现、ES6 Class
4. **异步编程**：事件循环、微任务宏任务、Promise实现
5. **函数式编程**：纯函数、高阶函数、数组方法实现
6. **性能优化**：防抖节流、内存管理、性能监控
7. **现代特性**：可选链、空值合并、BigInt、动态导入
8. **模块化**：CommonJS vs ES Modules
9. **错误处理**：try-catch、Promise错误处理、全局错误处理
10. **调试工具**：性能API、内存泄漏检测、代码分析

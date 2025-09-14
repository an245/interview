# 后端开发 核心概念与面试题

## 1. Node.js基础

### 核心概念
- **事件驱动**：基于事件循环的非阻塞I/O
- **单线程**：主线程单线程，I/O操作异步
- **模块系统**：CommonJS模块系统
- **包管理**：npm包管理器
- **异步编程**：回调、Promise、async/await

### 常问面试题

#### Q1: Node.js的特点和优势？
**答案：**
```javascript
// Node.js特点：
// 1. 事件驱动、非阻塞I/O
// 2. 单线程但高并发
// 3. 跨平台
// 4. 丰富的生态系统

// 事件驱动示例
const EventEmitter = require('events');

class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();

myEmitter.on('event', (data) => {
  console.log('Event received:', data);
});

myEmitter.emit('event', 'Hello World');

// 非阻塞I/O示例
const fs = require('fs');

// 阻塞方式（同步）
console.log('开始读取文件');
const data = fs.readFileSync('file.txt', 'utf8');
console.log('文件内容:', data);
console.log('读取完成');

// 非阻塞方式（异步）
console.log('开始读取文件');
fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) throw err;
  console.log('文件内容:', data);
});
console.log('读取完成'); // 这行会先执行

// 优势：
// 1. 高并发处理能力
// 2. 开发效率高
// 3. 前后端语言统一
// 4. 丰富的第三方库
// 5. 适合I/O密集型应用
```

#### Q2: Node.js的模块系统？
**答案：**
```javascript
// 1. CommonJS模块系统
// math.js - 模块定义
function add(a, b) {
  return a + b;
}

function subtract(a, b) {
  return a - b;
}

// 导出模块
module.exports = {
  add,
  subtract
};

// 或者
exports.add = add;
exports.subtract = subtract;

// main.js - 模块使用
const math = require('./math');
console.log(math.add(2, 3)); // 5

// 或者解构导入
const { add, subtract } = require('./math');
console.log(add(2, 3)); // 5

// 2. ES6模块系统
// math.mjs
export function add(a, b) {
  return a + b;
}

export function subtract(a, b) {
  return a - b;
}

// 默认导出
export default function multiply(a, b) {
  return a * b;
}

// main.mjs
import { add, subtract } from './math.mjs';
import multiply from './math.mjs';

console.log(add(2, 3)); // 5
console.log(multiply(2, 3)); // 6

// 3. 内置模块
const http = require('http');
const fs = require('fs');
const path = require('path');
const url = require('url');
const querystring = require('querystring');

// 4. 第三方模块
const express = require('express');
const mongoose = require('mongoose');
const lodash = require('lodash');

// 5. 模块缓存
// Node.js会缓存已加载的模块
const module1 = require('./module');
const module2 = require('./module');
console.log(module1 === module2); // true
```

## 2. Express框架

### 核心概念
- **路由**：URL路径和HTTP方法的映射
- **中间件**：请求处理管道
- **模板引擎**：服务端渲染
- **静态文件**：静态资源服务
- **错误处理**：统一错误处理

### 常问面试题

#### Q3: Express中间件机制？
**答案：**
```javascript
const express = require('express');
const app = express();

// 1. 应用级中间件
app.use((req, res, next) => {
  console.log('Time:', Date.now());
  next(); // 必须调用next()继续执行
});

// 2. 路由级中间件
app.use('/api', (req, res, next) => {
  console.log('API请求');
  next();
});

// 3. 错误处理中间件
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});

// 4. 内置中间件
app.use(express.json()); // 解析JSON
app.use(express.urlencoded({ extended: true })); // 解析URL编码
app.use(express.static('public')); // 静态文件

// 5. 第三方中间件
const cors = require('cors');
const helmet = require('helmet');

app.use(cors());
app.use(helmet());

// 6. 自定义中间件
function logger(req, res, next) {
  console.log(`${req.method} ${req.url} - ${new Date()}`);
  next();
}

function auth(req, res, next) {
  const token = req.headers.authorization;
  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }
  // 验证token逻辑
  next();
}

// 使用中间件
app.use(logger);
app.use('/api/protected', auth);

// 7. 中间件执行顺序
app.use((req, res, next) => {
  console.log('1. 第一个中间件');
  next();
});

app.use((req, res, next) => {
  console.log('2. 第二个中间件');
  next();
});

app.get('/', (req, res) => {
  console.log('3. 路由处理函数');
  res.send('Hello World');
});

// 输出顺序：1 → 2 → 3
```

## 3. 数据库操作

### 核心概念
- **MongoDB**：NoSQL文档数据库
- **Mongoose**：MongoDB对象建模工具
- **SQL数据库**：关系型数据库
- **ORM/ODM**：对象关系映射
- **连接池**：数据库连接管理

### 常问面试题

#### Q4: MongoDB和Mongoose使用？
**答案：**
```javascript
const mongoose = require('mongoose');

// 1. 连接数据库
mongoose.connect('mongodb://localhost:27017/myapp', {
  useNewUrlParser: true,
  useUnifiedTopology: true
});

// 2. 定义Schema
const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
    trim: true
  },
  email: {
    type: String,
    required: true,
    unique: true,
    lowercase: true
  },
  age: {
    type: Number,
    min: 0,
    max: 120
  },
  createdAt: {
    type: Date,
    default: Date.now
  }
});

// 3. 添加方法
userSchema.methods.getFullName = function() {
  return this.name;
};

// 4. 添加静态方法
userSchema.statics.findByEmail = function(email) {
  return this.findOne({ email });
};

// 5. 添加虚拟属性
userSchema.virtual('fullName').get(function() {
  return `${this.name} (${this.email})`;
});

// 6. 中间件
userSchema.pre('save', function(next) {
  console.log('保存前执行');
  next();
});

userSchema.post('save', function(doc) {
  console.log('保存后执行');
});

// 7. 创建模型
const User = mongoose.model('User', userSchema);

// 8. CRUD操作
// 创建
const user = new User({
  name: 'John Doe',
  email: 'john@example.com',
  age: 30
});

user.save((err, savedUser) => {
  if (err) return console.error(err);
  console.log('User saved:', savedUser);
});

// 查询
User.find({ age: { $gte: 18 } }, (err, users) => {
  if (err) return console.error(err);
  console.log('Users:', users);
});

// 更新
User.findByIdAndUpdate(
  userId,
  { age: 31 },
  { new: true },
  (err, updatedUser) => {
    if (err) return console.error(err);
    console.log('Updated user:', updatedUser);
  }
);

// 删除
User.findByIdAndDelete(userId, (err, deletedUser) => {
  if (err) return console.error(err);
  console.log('Deleted user:', deletedUser);
});

// 9. 聚合查询
User.aggregate([
  { $match: { age: { $gte: 18 } } },
  { $group: { _id: '$age', count: { $sum: 1 } } },
  { $sort: { _id: 1 } }
], (err, result) => {
  if (err) return console.error(err);
  console.log('Aggregation result:', result);
});
```

## 4. 异步编程

### 核心概念
- **回调函数**：异步操作完成后的处理函数
- **Promise**：异步操作的容器
- **async/await**：异步函数的语法糖
- **事件循环**：异步操作的处理机制
- **错误处理**：异步操作的错误处理

### 常问面试题

#### Q5: Node.js异步编程模式？
**答案：**
```javascript
const fs = require('fs').promises;
const axios = require('axios');

// 1. 回调函数模式
function readFileCallback(filename, callback) {
  fs.readFile(filename, 'utf8', (err, data) => {
    if (err) {
      callback(err, null);
    } else {
      callback(null, data);
    }
  });
}

// 使用回调
readFileCallback('file.txt', (err, data) => {
  if (err) {
    console.error('Error:', err);
  } else {
    console.log('Data:', data);
  }
});

// 2. Promise模式
function readFilePromise(filename) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, 'utf8', (err, data) => {
      if (err) {
        reject(err);
      } else {
        resolve(data);
      }
    });
  });
}

// 使用Promise
readFilePromise('file.txt')
  .then(data => {
    console.log('Data:', data);
  })
  .catch(err => {
    console.error('Error:', err);
  });

// 3. async/await模式
async function readFileAsync(filename) {
  try {
    const data = await fs.readFile(filename, 'utf8');
    console.log('Data:', data);
    return data;
  } catch (err) {
    console.error('Error:', err);
    throw err;
  }
}

// 使用async/await
readFileAsync('file.txt');

// 4. 并发处理
async function fetchMultipleData() {
  try {
    // 并发执行
    const [userData, postData, commentData] = await Promise.all([
      axios.get('/api/users'),
      axios.get('/api/posts'),
      axios.get('/api/comments')
    ]);
    
    return {
      users: userData.data,
      posts: postData.data,
      comments: commentData.data
    };
  } catch (err) {
    console.error('Error fetching data:', err);
    throw err;
  }
}

// 5. 错误处理
async function handleErrors() {
  try {
    const data = await readFileAsync('file.txt');
    return data;
  } catch (err) {
    if (err.code === 'ENOENT') {
      console.log('File not found');
    } else if (err.code === 'EACCES') {
      console.log('Permission denied');
    } else {
      console.log('Unknown error:', err.message);
    }
    throw err;
  }
}

// 6. 超时处理
function withTimeout(promise, timeout) {
  return Promise.race([
    promise,
    new Promise((_, reject) => {
      setTimeout(() => reject(new Error('Timeout')), timeout);
    })
  ]);
}

// 使用超时
withTimeout(readFileAsync('file.txt'), 5000)
  .then(data => console.log('Data:', data))
  .catch(err => console.error('Error:', err));

// 7. 重试机制
async function retry(fn, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (err) {
      if (i === maxRetries - 1) throw err;
      console.log(`Retry ${i + 1}/${maxRetries}`);
      await new Promise(resolve => setTimeout(resolve, 1000 * (i + 1)));
    }
  }
}

// 使用重试
retry(() => readFileAsync('file.txt'))
  .then(data => console.log('Data:', data))
  .catch(err => console.error('Error:', err));
```

## 总结

后端开发面试重点：
1. **Node.js基础**：事件驱动、非阻塞I/O、模块系统、异步编程
2. **Express框架**：路由、中间件、模板引擎、错误处理
3. **数据库操作**：MongoDB、Mongoose、SQL数据库、ORM/ODM
4. **异步编程**：回调、Promise、async/await、错误处理
5. **性能优化**：缓存、连接池、负载均衡、压缩、监控
6. **安全防护**：身份验证、授权、输入验证、SQL注入、XSS防护
7. **部署运维**：Docker、PM2、监控、日志
8. **最佳实践**：代码规范、架构设计、测试、文档
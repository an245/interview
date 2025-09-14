# 项目管理与工程化 核心概念与面试题

## 1. 项目管理基础

### 核心概念
- **项目生命周期**：项目从开始到结束的过程
- **需求管理**：需求收集、分析、变更管理
- **进度管理**：项目进度计划和控制
- **质量管理**：项目质量保证和控制
- **风险管理**：项目风险识别和控制

### 常问面试题

#### Q1: 项目管理的核心要素？
**答案：**
```
项目管理的核心要素：

1. 项目范围管理
   - 需求收集和分析
   - 范围定义和确认
   - 范围变更控制
   - 工作分解结构(WBS)

2. 项目时间管理
   - 活动定义和排序
   - 资源估算
   - 进度计划制定
   - 进度控制

3. 项目成本管理
   - 成本估算
   - 预算制定
   - 成本控制
   - 成本分析

4. 项目质量管理
   - 质量规划
   - 质量保证
   - 质量控制
   - 质量改进

5. 项目风险管理
   - 风险识别
   - 风险分析
   - 风险应对
   - 风险监控

6. 项目沟通管理
   - 沟通规划
   - 信息分发
   - 绩效报告
   - 干系人管理

7. 项目人力资源管理
   - 团队组建
   - 团队发展
   - 团队管理
   - 团队激励

8. 项目采购管理
   - 采购规划
   - 采购执行
   - 采购控制
   - 合同管理
```

#### Q2: 敏捷开发方法论？
**答案：**
```
敏捷开发核心原则：

1. 个体和交互胜过过程和工具
2. 可工作的软件胜过详尽的文档
3. 客户合作胜过合同谈判
4. 响应变化胜过遵循计划

敏捷开发实践：

1. Scrum框架
   - 角色：Product Owner、Scrum Master、Development Team
   - 事件：Sprint、Sprint Planning、Daily Scrum、Sprint Review、Retrospective
   - 工件：Product Backlog、Sprint Backlog、Increment

2. 看板方法
   - 可视化工作流
   - 限制在制品数量
   - 管理流动
   - 持续改进

3. 极限编程(XP)
   - 结对编程
   - 测试驱动开发
   - 持续集成
   - 重构

4. 用户故事
   - 作为[用户角色]
   - 我希望[功能描述]
   - 以便[业务价值]

5. 持续集成/持续部署(CI/CD)
   - 自动化构建
   - 自动化测试
   - 自动化部署
   - 持续监控
```

## 2. 前端工程化

### 核心概念
- **构建工具**：Webpack、Vite、Rollup
- **代码规范**：ESLint、Prettier、Husky
- **测试框架**：Jest、Cypress、Playwright
- **部署流程**：CI/CD、Docker、K8s
- **监控体系**：性能监控、错误监控

### 常问面试题

#### Q3: 前端工程化体系？
**答案：**
```javascript
// 1. 代码规范配置
// .eslintrc.js
module.exports = {
  extends: [
    'eslint:recommended',
    '@typescript-eslint/recommended',
    'plugin:react/recommended',
    'plugin:react-hooks/recommended'
  ],
  rules: {
    'no-console': 'warn',
    'no-debugger': 'error',
    'react/prop-types': 'off',
    '@typescript-eslint/no-unused-vars': 'error'
  },
  settings: {
    react: {
      version: 'detect'
    }
  }
};

// .prettierrc
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2
}

// 2. Git Hooks配置
// package.json
{
  "scripts": {
    "lint": "eslint src --ext .js,.jsx,.ts,.tsx",
    "lint:fix": "eslint src --ext .js,.jsx,.ts,.tsx --fix",
    "format": "prettier --write src/**/*.{js,jsx,ts,tsx,json,css,md}",
    "test": "jest",
    "test:coverage": "jest --coverage",
    "build": "webpack --mode production",
    "dev": "webpack serve --mode development"
  },
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "pre-push": "npm run test"
    }
  },
  "lint-staged": {
    "src/**/*.{js,jsx,ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ]
  }
}

// 3. 测试配置
// jest.config.js
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/src/setupTests.ts'],
  moduleNameMapping: {
    '^@/(.*)$': '<rootDir>/src/$1'
  },
  collectCoverageFrom: [
    'src/**/*.{js,jsx,ts,tsx}',
    '!src/**/*.d.ts',
    '!src/index.tsx'
  ],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  }
};

// 4. 构建配置
// webpack.config.js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  entry: './src/index.tsx',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[contenthash].js',
    clean: true
  },
  module: {
    rules: [
      {
        test: /\.(ts|tsx)$/,
        use: 'ts-loader',
        exclude: /node_modules/
      },
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader']
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './public/index.html'
    }),
    new MiniCssExtractPlugin({
      filename: '[name].[contenthash].css'
    })
  ]
};

// 5. 部署配置
// Dockerfile
FROM node:16-alpine as builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

// 6. CI/CD配置
// .github/workflows/deploy.yml
name: Deploy
on:
  push:
    branches: [main]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: '16'
      - run: npm ci
      - run: npm run lint
      - run: npm run test
      - run: npm run build
      - name: Deploy to AWS
        run: aws s3 sync dist/ s3://my-bucket --delete
```

## 3. 团队协作

### 核心概念
- **版本控制**：Git工作流、分支管理
- **代码审查**：Pull Request、Code Review
- **文档管理**：技术文档、API文档
- **沟通协作**：团队沟通、会议管理
- **知识分享**：技术分享、经验总结

### 常问面试题

#### Q4: Git工作流和分支管理？
**答案：**
```
Git工作流策略：

1. Git Flow
   - master: 生产环境分支
   - develop: 开发环境分支
   - feature: 功能开发分支
   - release: 发布准备分支
   - hotfix: 紧急修复分支

2. GitHub Flow
   - master: 主分支
   - feature: 功能分支
   - 通过Pull Request合并

3. GitLab Flow
   - master: 主分支
   - production: 生产分支
   - feature: 功能分支

分支管理最佳实践：

1. 分支命名规范
   - feature/user-authentication
   - bugfix/login-error
   - hotfix/security-patch
   - release/v1.2.0

2. 提交信息规范
   - feat: 新功能
   - fix: 修复bug
   - docs: 文档更新
   - style: 代码格式调整
   - refactor: 代码重构
   - test: 测试相关
   - chore: 构建过程或辅助工具的变动

3. Pull Request流程
   - 创建功能分支
   - 开发功能
   - 提交代码
   - 创建Pull Request
   - 代码审查
   - 合并代码

4. 代码审查要点
   - 代码质量
   - 功能正确性
   - 性能考虑
   - 安全性
   - 可维护性
```

#### Q5: 团队协作工具和方法？
**答案：**
```
团队协作工具：

1. 项目管理工具
   - Jira: 项目管理和问题跟踪
   - Trello: 看板式项目管理
   - Asana: 任务和项目管理
   - Notion: 文档和知识管理

2. 代码协作工具
   - GitHub: 代码托管和协作
   - GitLab: 代码托管和CI/CD
   - Bitbucket: 代码托管
   - CodePen: 前端代码分享

3. 沟通工具
   - Slack: 团队沟通
   - Microsoft Teams: 企业协作
   - Discord: 开发者社区
   - 钉钉/企业微信: 国内企业协作

4. 设计协作工具
   - Figma: 界面设计协作
   - Sketch: 界面设计
   - Adobe XD: 用户体验设计
   - Zeplin: 设计交付

5. 文档协作工具
   - Confluence: 企业知识管理
   - Notion: 文档和知识管理
   - GitBook: 技术文档
   - 语雀: 知识管理

团队协作方法：

1. 每日站会
   - 昨天做了什么
   - 今天计划做什么
   - 遇到什么阻碍

2. 迭代回顾
   - 做得好的地方
   - 需要改进的地方
   - 下一步行动计划

3. 技术分享
   - 新技术学习
   - 项目经验总结
   - 最佳实践分享

4. 代码审查
   - 代码质量检查
   - 知识传递
   - 团队标准统一
```

## 4. 质量保证

### 核心概念
- **测试策略**：单元测试、集成测试、端到端测试
- **代码质量**：代码规范、复杂度控制
- **性能监控**：性能指标、监控告警
- **错误处理**：错误捕获、日志记录
- **安全防护**：安全扫描、漏洞修复

### 常问面试题

#### Q6: 前端质量保证体系？
**答案：**
```javascript
// 1. 测试策略
// 单元测试
import { render, screen, fireEvent } from '@testing-library/react';
import Button from './Button';

describe('Button', () => {
  test('renders button with text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  test('calls onClick when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    fireEvent.click(screen.getByText('Click me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});

// 集成测试
import { render, screen, waitFor } from '@testing-library/react';
import { rest } from 'msw';
import { setupServer } from 'msw/node';
import UserList from './UserList';

const server = setupServer(
  rest.get('/api/users', (req, res, ctx) => {
    return res(ctx.json([
      { id: 1, name: 'John Doe' },
      { id: 2, name: 'Jane Smith' }
    ]));
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

test('displays user list', async () => {
  render(<UserList />);
  await waitFor(() => {
    expect(screen.getByText('John Doe')).toBeInTheDocument();
    expect(screen.getByText('Jane Smith')).toBeInTheDocument();
  });
});

// 端到端测试
import { test, expect } from '@playwright/test';

test('user can login', async ({ page }) => {
  await page.goto('/login');
  await page.fill('[data-testid=email]', 'user@example.com');
  await page.fill('[data-testid=password]', 'password');
  await page.click('[data-testid=login-button]');
  await expect(page).toHaveURL('/dashboard');
});

// 2. 代码质量检查
// .eslintrc.js
module.exports = {
  extends: ['eslint:recommended', '@typescript-eslint/recommended'],
  rules: {
    'complexity': ['error', 10],
    'max-lines': ['error', 300],
    'max-lines-per-function': ['error', 50],
    'max-params': ['error', 4],
    'no-console': 'warn',
    'no-debugger': 'error'
  }
};

// 3. 性能监控
// performance.js
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
      
      this.sendMetrics();
    });

    // 监控资源加载
    const observer = new PerformanceObserver((list) => {
      list.getEntries().forEach(entry => {
        if (entry.entryType === 'resource') {
          this.metrics.resourceLoadTime = entry.duration;
        }
      });
    });

    observer.observe({ entryTypes: ['resource'] });
  }

  sendMetrics() {
    fetch('/api/metrics', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(this.metrics)
    });
  }
}

// 4. 错误监控
// error-handler.js
class ErrorHandler {
  constructor() {
    this.setupGlobalErrorHandling();
  }

  setupGlobalErrorHandling() {
    // 捕获JavaScript错误
    window.addEventListener('error', (event) => {
      this.reportError({
        type: 'javascript',
        message: event.message,
        filename: event.filename,
        lineno: event.lineno,
        colno: event.colno,
        stack: event.error?.stack
      });
    });

    // 捕获Promise错误
    window.addEventListener('unhandledrejection', (event) => {
      this.reportError({
        type: 'promise',
        message: event.reason?.message || 'Unhandled Promise Rejection',
        stack: event.reason?.stack
      });
    });
  }

  reportError(errorInfo) {
    fetch('/api/errors', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        ...errorInfo,
        timestamp: new Date().toISOString(),
        userAgent: navigator.userAgent,
        url: window.location.href
      })
    });
  }
}

// 5. 安全扫描
// security.js
class SecurityScanner {
  constructor() {
    this.scan();
  }

  scan() {
    // 检查HTTPS
    if (location.protocol !== 'https:') {
      console.warn('Site is not using HTTPS');
    }

    // 检查CSP
    const csp = document.querySelector('meta[http-equiv="Content-Security-Policy"]');
    if (!csp) {
      console.warn('No Content Security Policy found');
    }

    // 检查敏感信息
    this.checkSensitiveData();
  }

  checkSensitiveData() {
    const scripts = document.querySelectorAll('script');
    scripts.forEach(script => {
      if (script.textContent.includes('password') || 
          script.textContent.includes('api_key')) {
        console.warn('Potential sensitive data found in script');
      }
    });
  }
}
```

## 5. 部署运维

### 核心概念
- **部署策略**：蓝绿部署、滚动部署、金丝雀部署
- **容器化**：Docker、Kubernetes
- **监控告警**：系统监控、应用监控
- **日志管理**：日志收集、分析、存储
- **备份恢复**：数据备份、灾难恢复

### 常问面试题

#### Q7: 前端部署策略？
**答案：**
```
部署策略：

1. 蓝绿部署
   - 维护两套相同的生产环境
   - 一套运行当前版本，一套部署新版本
   - 切换流量到新版本
   - 优点：快速回滚，零停机时间
   - 缺点：资源消耗大

2. 滚动部署
   - 逐步替换旧版本实例
   - 每次替换一部分实例
   - 优点：资源消耗小
   - 缺点：部署时间长，可能出现版本不一致

3. 金丝雀部署
   - 先部署到小部分用户
   - 监控指标正常后全量部署
   - 优点：风险可控
   - 缺点：部署复杂

4. A/B测试部署
   - 同时运行多个版本
   - 根据用户特征分发流量
   - 优点：可以对比效果
   - 缺点：需要复杂的流量控制

部署工具：

1. 传统部署
   - FTP/SFTP上传
   - 手动部署脚本
   - 简单但容易出错

2. 自动化部署
   - Jenkins: 持续集成/持续部署
   - GitHub Actions: 基于GitHub的CI/CD
   - GitLab CI: 基于GitLab的CI/CD
   - Azure DevOps: 微软的DevOps平台

3. 容器化部署
   - Docker: 容器化应用
   - Kubernetes: 容器编排
   - Docker Compose: 多容器应用

4. 云平台部署
   - AWS: S3 + CloudFront
   - Azure: Static Web Apps
   - Google Cloud: Firebase Hosting
   - 阿里云: OSS + CDN
```

#### Q8: 监控和运维体系？
**答案：**
```
监控体系：

1. 系统监控
   - CPU使用率
   - 内存使用率
   - 磁盘使用率
   - 网络流量
   - 工具：Prometheus、Grafana、Zabbix

2. 应用监控
   - 响应时间
   - 吞吐量
   - 错误率
   - 可用性
   - 工具：New Relic、DataDog、APM

3. 前端监控
   - 页面加载时间
   - 用户交互响应时间
   - JavaScript错误
   - 资源加载失败
   - 工具：Sentry、Bugsnag、LogRocket

4. 业务监控
   - 用户行为
   - 转化率
   - 业务指标
   - 工具：Google Analytics、Mixpanel

日志管理：

1. 日志收集
   - 应用日志
   - 系统日志
   - 访问日志
   - 错误日志

2. 日志存储
   - 本地文件
   - 数据库
   - 日志服务
   - 工具：ELK Stack、Fluentd

3. 日志分析
   - 实时分析
   - 历史分析
   - 趋势分析
   - 异常检测

告警机制：

1. 告警规则
   - 阈值告警
   - 趋势告警
   - 异常告警
   - 业务告警

2. 告警渠道
   - 邮件
   - 短信
   - 电话
   - 即时消息

3. 告警处理
   - 自动处理
   - 人工处理
   - 升级机制
   - 处理记录
```

## 总结

项目管理与工程化面试重点：
1. **项目管理基础**：项目生命周期、需求管理、进度管理、质量管理
2. **敏捷开发**：Scrum、看板、XP、用户故事、CI/CD
3. **前端工程化**：构建工具、代码规范、测试框架、部署流程
4. **团队协作**：版本控制、代码审查、文档管理、沟通协作
5. **质量保证**：测试策略、代码质量、性能监控、错误处理
6. **部署运维**：部署策略、容器化、监控告警、日志管理
7. **工具链**：开发工具、构建工具、部署工具、监控工具
8. **最佳实践**：流程规范、标准制定、经验总结、持续改进

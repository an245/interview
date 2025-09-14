# Vite & Webpack 核心概念与面试题

## 1. Webpack基础

### 核心概念
- **入口(Entry)**：打包的起点
- **输出(Output)**：打包后的文件输出位置
- **加载器(Loader)**：处理非JavaScript文件
- **插件(Plugin)**：扩展webpack功能
- **模式(Mode)**：开发模式或生产模式

### 常问面试题

#### Q1: Webpack的工作原理？
**答案：**
```javascript
// webpack.config.js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  // 入口文件
  entry: './src/index.js',
  
  // 输出配置
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.[contenthash].js',
    clean: true
  },
  
  // 模式
  mode: 'production',
  
  // 模块处理
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
        }
      },
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader']
      },
      {
        test: /\.(png|svg|jpg|jpeg|gif)$/i,
        type: 'asset/resource'
      }
    ]
  },
  
  // 插件
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
    new MiniCssExtractPlugin({
      filename: 'styles.[contenthash].css'
    })
  ],
  
  // 开发服务器
  devServer: {
    static: './dist',
    hot: true,
    port: 3000
  }
};

// Webpack工作流程：
// 1. 从入口文件开始
// 2. 递归解析依赖关系
// 3. 使用loader处理文件
// 4. 使用plugin扩展功能
// 5. 生成输出文件
```

#### Q2: Webpack的Loader和Plugin区别？
**答案：**
```javascript
// Loader - 处理文件转换
module.exports = {
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: [
          'style-loader',    // 将CSS注入到DOM
          'css-loader',      // 解析CSS文件
          'sass-loader'      // 编译Sass文件
        ]
      },
      {
        test: /\.ts$/,
        use: 'ts-loader'
      }
    ]
  }
};

// Plugin - 扩展功能
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');

module.exports = {
  plugins: [
    new CleanWebpackPlugin(), // 清理输出目录
    new HtmlWebpackPlugin({   // 生成HTML文件
      template: './src/index.html'
    })
  ]
};

// 区别：
// Loader:
// - 处理文件转换
// - 在模块加载时执行
// - 链式调用
// - 输入文件，输出处理后的文件

// Plugin:
// - 扩展webpack功能
// - 在webpack运行时的特定时机执行
// - 可以访问webpack的完整生命周期
// - 可以修改输出文件
```

## 2. Webpack优化

### 核心概念
- **代码分割**：将代码分割成多个chunk
- **Tree Shaking**：移除未使用的代码
- **缓存**：利用浏览器缓存
- **压缩**：代码压缩和优化
- **懒加载**：按需加载模块

### 常问面试题

#### Q3: Webpack性能优化策略？
**答案：**
```javascript
// webpack.config.js
const path = require('path');
const webpack = require('webpack');

module.exports = {
  // 1. 代码分割
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          chunks: 'all'
        },
        common: {
          name: 'common',
          minChunks: 2,
          chunks: 'all',
          enforce: true
        }
      }
    }
  },
  
  // 2. Tree Shaking
  optimization: {
    usedExports: true,
    sideEffects: false
  },
  
  // 3. 缓存
  cache: {
    type: 'filesystem',
    buildDependencies: {
      config: [__filename]
    }
  },
  
  // 4. 压缩
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        terserOptions: {
          compress: {
            drop_console: true
          }
        }
      })
    ]
  },
  
  // 5. 懒加载
  // 动态导入
  // import('./module').then(module => {
  //   // 使用模块
  // });
  
  // 6. 预加载
  module: {
    rules: [
      {
        test: /\.js$/,
        use: {
          loader: 'babel-loader',
          options: {
            plugins: [
              '@babel/plugin-syntax-dynamic-import'
            ]
          }
        }
      }
    ]
  }
};

// 7. 开发环境优化
module.exports = {
  mode: 'development',
  devtool: 'eval-cheap-module-source-map',
  devServer: {
    hot: true,
    compress: true,
    historyApiFallback: true
  }
};

// 8. 生产环境优化
module.exports = {
  mode: 'production',
  devtool: 'source-map',
  optimization: {
    minimize: true,
    splitChunks: {
      chunks: 'all'
    }
  }
};
```

## 3. Vite基础

### 核心概念
- **ES模块**：基于ES模块的开发服务器
- **热更新**：快速的热模块替换
- **预构建**：依赖预构建
- **插件系统**：基于Rollup的插件系统
- **构建工具**：基于Rollup的生产构建

### 常问面试题

#### Q4: Vite的工作原理？
**答案：**
```javascript
// vite.config.js
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import { resolve } from 'path';

export default defineConfig({
  // 插件
  plugins: [vue()],
  
  // 开发服务器
  server: {
    port: 3000,
    open: true,
    cors: true,
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, '')
      }
    }
  },
  
  // 构建配置
  build: {
    outDir: 'dist',
    assetsDir: 'assets',
    sourcemap: false,
    minify: 'terser',
    rollupOptions: {
      output: {
        chunkFileNames: 'js/[name]-[hash].js',
        entryFileNames: 'js/[name]-[hash].js',
        assetFileNames: '[ext]/[name]-[hash].[ext]'
      }
    }
  },
  
  // 路径别名
  resolve: {
    alias: {
      '@': resolve(__dirname, 'src'),
      'components': resolve(__dirname, 'src/components')
    }
  },
  
  // CSS配置
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `@import "@/styles/variables.scss";`
      }
    }
  }
});

// Vite工作原理：
// 1. 开发环境：使用ES模块，无需打包
// 2. 生产环境：使用Rollup打包
// 3. 热更新：基于ES模块的HMR
// 4. 预构建：将CommonJS转换为ES模块
```

#### Q5: Vite vs Webpack的区别？
**答案：**
```javascript
// Vite优势：
// 1. 开发服务器启动快
// 2. 热更新速度快
// 3. 基于ES模块
// 4. 配置简单

// Vite配置示例
// vite.config.js
export default defineConfig({
  plugins: [vue()],
  server: {
    port: 3000,
    proxy: {
      '/api': 'http://localhost:8080'
    }
  }
});

// Webpack优势：
// 1. 生态成熟
// 2. 插件丰富
// 3. 配置灵活
// 4. 社区支持好

// Webpack配置示例
// webpack.config.js
module.exports = {
  entry: './src/index.js',
  module: {
    rules: [
      {
        test: /\.vue$/,
        use: 'vue-loader'
      }
    ]
  },
  plugins: [
    new VueLoaderPlugin()
  ]
};

// 主要区别：
// 1. 开发体验：
//    - Vite: 启动快，热更新快
//    - Webpack: 启动慢，热更新相对慢

// 2. 构建方式：
//    - Vite: 开发环境不打包，生产环境用Rollup
//    - Webpack: 开发和生产环境都打包

// 3. 配置复杂度：
//    - Vite: 配置简单
//    - Webpack: 配置复杂但灵活

// 4. 生态：
//    - Vite: 生态相对较新
//    - Webpack: 生态成熟，插件丰富
```

## 4. 构建优化

### 核心概念
- **代码分割**：按需加载代码
- **Tree Shaking**：移除未使用代码
- **压缩优化**：代码和资源压缩
- **缓存策略**：利用浏览器缓存
- **预加载**：资源预加载

### 常问面试题

#### Q6: 如何优化构建性能？
**答案：**
```javascript
// Webpack优化
module.exports = {
  // 1. 使用缓存
  cache: {
    type: 'filesystem',
    buildDependencies: {
      config: [__filename]
    }
  },
  
  // 2. 并行处理
  module: {
    rules: [
      {
        test: /\.js$/,
        use: [
          {
            loader: 'thread-loader',
            options: {
              workers: 2
            }
          },
          'babel-loader'
        ]
      }
    ]
  },
  
  // 3. 代码分割
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          chunks: 'all'
        }
      }
    }
  },
  
  // 4. 压缩优化
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        parallel: true,
        terserOptions: {
          compress: {
            drop_console: true
          }
        }
      })
    ]
  }
};

// Vite优化
export default defineConfig({
  // 1. 预构建优化
  optimizeDeps: {
    include: ['vue', 'vue-router'],
    exclude: ['@vueuse/core']
  },
  
  // 2. 构建优化
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['vue', 'vue-router'],
          utils: ['lodash', 'dayjs']
        }
      }
    }
  },
  
  // 3. 开发服务器优化
  server: {
    fs: {
      strict: false
    }
  }
});

// 通用优化策略：
// 1. 使用缓存
// 2. 并行处理
// 3. 代码分割
// 4. 压缩优化
// 5. 预加载
// 6. 懒加载
// 7. 资源优化
// 8. 监控分析
```

## 5. 插件开发

### 核心概念
- **插件机制**：扩展构建工具功能
- **生命周期**：插件执行时机
- **API接口**：插件开发接口
- **配置选项**：插件配置参数
- **错误处理**：插件错误处理

### 常问面试题

#### Q7: 如何开发Webpack插件？
**答案：**
```javascript
// 自定义Webpack插件
class MyPlugin {
  constructor(options) {
    this.options = options || {};
  }
  
  apply(compiler) {
    // 在编译开始时执行
    compiler.hooks.compile.tap('MyPlugin', (params) => {
      console.log('编译开始');
    });
    
    // 在资源输出前执行
    compiler.hooks.emit.tapAsync('MyPlugin', (compilation, callback) => {
      // 修改输出文件
      const source = 'console.log("Hello from MyPlugin");';
      compilation.assets['my-plugin.js'] = {
        source: () => source,
        size: () => source.length
      };
      
      callback();
    });
    
    // 在编译完成后执行
    compiler.hooks.done.tap('MyPlugin', (stats) => {
      console.log('编译完成');
    });
  }
}

// 使用插件
module.exports = {
  plugins: [
    new MyPlugin({
      option1: 'value1',
      option2: 'value2'
    })
  ]
};

// 复杂插件示例
class BundleAnalyzerPlugin {
  constructor(options = {}) {
    this.options = options;
  }
  
  apply(compiler) {
    compiler.hooks.emit.tapAsync('BundleAnalyzerPlugin', (compilation, callback) => {
      const stats = compilation.getStats().toJson();
      const analysis = this.analyzeBundle(stats);
      
      // 生成分析报告
      const report = this.generateReport(analysis);
      
      compilation.assets['bundle-analysis.html'] = {
        source: () => report,
        size: () => report.length
      };
      
      callback();
    });
  }
  
  analyzeBundle(stats) {
    // 分析bundle大小
    const modules = stats.modules;
    const totalSize = modules.reduce((sum, module) => sum + module.size, 0);
    
    return {
      totalSize,
      modules: modules.map(module => ({
        name: module.name,
        size: module.size,
        percentage: (module.size / totalSize * 100).toFixed(2)
      }))
    };
  }
  
  generateReport(analysis) {
    return `
      <!DOCTYPE html>
      <html>
        <head>
          <title>Bundle Analysis</title>
        </head>
        <body>
          <h1>Bundle Analysis</h1>
          <p>Total Size: ${analysis.totalSize} bytes</p>
          <ul>
            ${analysis.modules.map(module => 
              `<li>${module.name}: ${module.size} bytes (${module.percentage}%)</li>`
            ).join('')}
          </ul>
        </body>
      </html>
    `;
  }
}
```

## 6. 部署优化

### 核心概念
- **静态资源**：CSS、JS、图片等资源
- **CDN加速**：内容分发网络
- **缓存策略**：浏览器缓存配置
- **压缩优化**：资源压缩
- **版本控制**：文件版本管理

### 常问面试题

#### Q8: 如何优化部署性能？
**答案：**
```javascript
// 1. 静态资源优化
module.exports = {
  output: {
    filename: '[name].[contenthash].js',
    chunkFilename: '[name].[contenthash].chunk.js',
    assetModuleFilename: 'assets/[name].[contenthash][ext]'
  },
  
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          chunks: 'all'
        }
      }
    }
  }
};

// 2. 压缩配置
const CompressionPlugin = require('compression-webpack-plugin');

module.exports = {
  plugins: [
    new CompressionPlugin({
      algorithm: 'gzip',
      test: /\.(js|css|html|svg)$/,
      threshold: 8192,
      minRatio: 0.8
    })
  ]
};

// 3. 环境变量配置
const webpack = require('webpack');

module.exports = {
  plugins: [
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV),
      'process.env.API_URL': JSON.stringify(process.env.API_URL)
    })
  ]
};

// 4. 部署脚本
// package.json
{
  "scripts": {
    "build": "webpack --mode production",
    "build:analyze": "webpack --mode production --analyze",
    "deploy": "npm run build && aws s3 sync dist/ s3://my-bucket --delete"
  }
}

// 5. CDN配置
module.exports = {
  output: {
    publicPath: 'https://cdn.example.com/'
  }
};

// 6. 缓存策略
// .htaccess
<IfModule mod_expires.c>
  ExpiresActive on
  ExpiresByType text/css "access plus 1 year"
  ExpiresByType application/javascript "access plus 1 year"
  ExpiresByType image/png "access plus 1 year"
  ExpiresByType image/jpg "access plus 1 year"
  ExpiresByType image/jpeg "access plus 1 year"
</IfModule>

// 7. 监控配置
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

module.exports = {
  plugins: [
    new BundleAnalyzerPlugin({
      analyzerMode: 'static',
      openAnalyzer: false,
      reportFilename: 'bundle-report.html'
    })
  ]
};
```

## 总结

Vite & Webpack面试重点：
1. **Webpack基础**：工作原理、Loader、Plugin、配置
2. **Webpack优化**：代码分割、Tree Shaking、缓存、压缩
3. **Vite基础**：工作原理、配置、插件系统
4. **Vite vs Webpack**：区别、优势、适用场景
5. **构建优化**：性能优化、开发体验优化
6. **插件开发**：自定义插件、生命周期、API
7. **部署优化**：静态资源、CDN、缓存、压缩
8. **最佳实践**：配置规范、性能监控、错误处理

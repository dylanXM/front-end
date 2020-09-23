## webapck基本

### 安装

```js
npm i webpack webpack-cli -g
```

### 基本配置

webpack基本配置共分五个模块：

​	entry：

​	output:

​	module:

​	plugins: 

​	mode: 

```js
// webpack.config.js
const path = require('path');

module.exports = {
    // entry
    entry: '',   // 可为字符串，数组，对象
    // output
    output: {
        filaname: 'built.js',   // 打包后的文件名
        path: path.resolve(__dirname, 'build'), // 打包后的文件路径
    },
    // loader
    module: {
        rules:[
            
        ]
    },
    // plugin
    plugins: [
        
    ],
    // mode
    mode: 'development', // 'development'为开发模式， 'production' 为生产模式
}
```



## 开发模式配置

### 开发模式目的

1. 更好更快地打包
2. 更方便调试

### 打包样式文件

1. 打包CSS文件

安装loader：

```powershell
npm i css-loader style-loader -D
```

配置：

```js
module.exports = {
   // ...
    module: {
        rules: [
            {
                test: /\.css$/,   // 匹配以CSS结尾的文件
                use: [
                    'style-loader',  // 将加载后的内容以style标签的形式插入到html文件内
                    'css-loader'   // 加载CSS文件
                ]
            }
        ]
    }
}
```

2. 打包LESS文件

安装loader：

```powershell
npm i css-loader style-loader less-loader -D
```

配置：

```js
module.exports = {
   // ...
    module: {
        rules: [
            {
                test: /\.css$/,   // 匹配以CSS结尾的文件
                use: [
                    'style-loader',  // 将加载后的内容以style标签的形式插入到html文件内
                    'css-loader',   // 加载CSS
                    'less-loader' // 将less转换为css
                ]
            }
        ]
    }
}
```

### 打包img文件

1. 打包样式文件中img文件

安装loader

```powershell
npm i url-loader file-loader -D
```

配置：

```js
module.exports = {
   // ...
    module: {
        rules: [
            {
                test: /\.(png|jpg|jpeg|gif)$/,   // 匹配以png|jpg|jpeg|gif结尾的文件
                loader: 'url-loader',
              	options: {
                  limit: 10 * 1024, // 小于10kb会用base64编码处理
                  name: '[hash:10].[ext]', // 打包后的文件命名为hash命名，设置为取hash的前10位
                  esModule: false, // 关闭es模块化，使用commonJs模块化
                }
            }
        ]
    }
}
```

2. 关联html中的的img文件

安装loader：

```js
npm i html-loader -D
```

配置：

```js
module.exports = {
   // ...
    module: {
        rules: [
            {
                test: /\.html$/,   // 匹配以html结尾的文件
                loader: 'html-loader'
            }
        ]
    }
}
```

### 打包其他静态文件（如字体文件）

1. 安装loader

```powershell
npm i file-loader -D
```

2. 配置

```js
module.exports = {
   // ...
    module: {
        rules: [
            {
                test: /\.(font|...)$/,   // 匹配以font|...结尾的文件
              	exclude: /\.(css|less|png|jpg|jpeg|gif|html)$/, // 排除以 css|less|png|jpg|jpeg|gif|html结尾的文件
                loader: 'file-loader'
            }
        ]
    }
}
```

### html模板

安装plugin

```powershell
npm i html-webpack-plugin -D 
```

配置

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  // ...
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'  //  以此目录下的index.html文件为模板
    })
  ]
}
```

### 本地运行服务器

不会生成文件，只在内存中存在

1. 安装

```powershell
npm i webpack-dev-server -D
```

2. 配置

```js
module.exports = {
   // ...
   devServer: {
     contentBase: path.resolve(__dirname, 'build'), // 输出路径
     conpress: true, // 压缩
     port: 3000, // 服务开启后的端口号
     open: true, // 服务启动后自动打开浏览器
   }
}
```

3. 运行

```powershell
npx webpack-dev-server
```

### 优化

1. HMR （Hot Module Replacement）

一个模块发生变化，只会重新打包这一个模块

配置

```js
module.exports = {
   entry: ['./src/index.js', './src/index.html'], // html文件开启HMR
   // ...
   devServer: {
     contentBase: path.resolve(__dirname, 'build'), // 输出路径
     conpress: true, // 压缩
     port: 3000, // 服务开启后的端口号
     open: true, // 服务启动后自动打开浏览器
     hot: true, // 样式文件开启HMR，js文件不会开启，html文件不会开启
   }
}

// index.js 添加支持HMR的代码
// 只能处理非入口文件HMR
if (module.hot) {
  module.hot.accept('./print.js', function () {
    // 方法会监听 print.js 文件的变化，一旦发生变化，其他模块不会重新打包构建
    // do something
  })
}
```

2. 开发环境/生产环境 调试代码 （sourceMap）

提供源代码到构建代码映射的技术（如果构建后代码出错了，通过映射关系会追踪到源代码的错误）

 [inline-|hidden-|eval-][nosources-][cheap-[module-]]source-map

内联 和 外部 的区别：

 	1. 内联会在内部生成映射代码，外部生成了新的文件 
 	2. 内联构建速度更快

source-map: 外部

​	错误代码的准确信息 和 源代码的错误位置

inline-source-map：内联

​	只生成一个内联 source-map

​	错误代码的准确信息 和 源代码的错误位置

hidden-source-map：外部

​	错误代码的准确信息 和 构建后代码的错误位置，不能追踪到源代码的位置

eval-source-map：内联

​	每一个文件都生成对应的source-map，都在eval函数中

​	错误代码的准确信息 和 源代码的错误位置

nosources-source-map: 外部

​	错误代码的准确信息，但是没有任何源代码信息

cheap-source-map: 外部

​	错误代码的准确信息，但是错误在一整行，不能具体到列

cheap-module-source-map：外部

​	错误代码的准确信息，但是错误在一整行，不能具体到列



开发环境：速度快，测试更友好

​	速度快：eval > inline > cheap

​		eval-cheap-source-map

​		eval-source-map

​	调试更友好：

​		source-map

​		cheap-module-source-map

​		cheap-source-map

​	综合：eval-source-map -- react 和 vue 的脚手架默认是 eval-source-map

生产环境：源代码要不要隐藏？调试要不要更友好

​	内联会让代码体积更大，生产环境中不考虑

​	no source-source-map: 代码全部隐藏

​	hidden-source-map：只隐藏源代码

​	综合： source-map

配置：

```js
module.exports = {
  // ...
  devtool: 'source-map'
}
```



## 开发环境配置

### 开发环境的目标

1. 更好更快地打包

### 提取CSS为单个目录/单个文件

安装插件

```js
npm i mini-css-extract-plugin -D
```

配置

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

module.exports = {
  // ...
  module: {
    rules: [
      test: /\.css$/,
      use: [
      	// 'style-loader',
        // 默认在生成文件下生成main.css并引入
      	MiniCssExtractPlugin.loader,
      	'css-loader'
    	]
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      // 在生成文件下重命名
      filename: 'css/built.css'
    })
  ]
}
```

### css各个浏览起的兼容性

安装：

```powershell
npm i postcss-loader postcss-preset-env -D
```

配置：

```js
// webpack.config.js
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

// package.json browserslist默认是production，如果想要用development
process.env.NODE_ENV = 'development';

module.exports = {
  // ...
  module: {
    rules: [
      test: /\.css$/,
      use: [
      	// 'style-loader',
        // 默认在生成文件下生成main.css并引入
      	MiniCssExtractPlugin.loader,
      	'css-loader',
        {
        	loader: 'postcss-loader',
        	options: {
        		ident: 'postcss',
        		plugins: () => [
					require('postcss-preset-env')()      
      			]
        	}
        }
    	]
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      // 在生成文件下重命名
      filename: 'css/built.css'
    })
  ]
}

// package.json
{
    // ...
    "browserslist": {
        // 兼容 chrome firefox safari 最近一个版本
		"development": [
            "last 1 chrome version",
            "last 1 firefox version",
            "last 1 safari version"
        ],
        // 兼容浏览器的最近0.2个版本   
        "production": [
            ">0.2%",
            "not dead",
            "not op_mini all"
        ]
    }
}
```

### 压缩CSS代码

安装插件

```powershell
npm i optimize-css-assets-webpack-plugin -D
```

配置

```js
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');

module.exports = {
  // ...
  module: {
    reles: [
      {
        test: /\.css$/,
        use: [
        	// ...
        ]
      }
    ]
  },
  plugins: [
      new OptimizeCssAssetsWebpackPlugin()
  ]
}
```

### Js语法规范性检查

安装：

```powershell
// 使用 eslint
npm i eslint-loader eslint -D
// 使用 airbnb 代码风格
npm i eslint-config-airbnb-base eslint-plugin-impoty -D
```

配置：

```js
// webpack.config.js
module.exports = {
    rules: [
        {
            test: /\.js$/,
            exclude: /node_module/,
            loader: 'eslint-loader',
          	enforce: 'pre',    // 优先执行
            options: {
                fix: true // 代码自动改正
            }
        }
    ]
}


// package.json
{
    // ...
    "eslintConfig": {
        "extends": 'airbnb-base' // 继承 airbnb-base 风格的规则
    }
}
```



### Js兼容性

安装：

```powershell
// 只能处理基本的ES6语法， 不能处理Promise等语法
npm i babel-loader @babel/preset-env -D
// 处理全部 但是包包含了所有的处理方法，并不是都是我们所需要的
npm i @babel-polyfill -D
// 按需处理
npm i core-js -D
```

配置

```js
module.exports = {
  module:{
    rules: [
        {
            test: /\.js$/,
            exclude: /node_module/,
            loader: 'babel-loader',
            options: {
                presets: [
                  [
                  	'@babel/preser-env',
                    {
                      useBuildIns: 'usage', // 使用corejs
                      corejs: { version: 3}， // corejs的版本
                      target: { // 对哪些浏览器的版本生效
                        chrome: '60', // chrome 60 版本及以上
                        firefox: '50' // firefox 50 版本及以上
                      }
                    }
                  ]
                ]
            }
        }
    ]
  }
};

// 注意： 一个文件只能被一个loader处理，当一个文件需要被多个loader进行处理时，需要注意处理的先后顺序，
// js 先执行 eslint  再执行 babel
// 在 babel 中添加了 "enforce: 'pre'"
```

### 压缩Js

mode 为 'production' 会自动压缩js， 内置了uglifyjs-webpack-plugin

### 保存图片为单个文件夹

配置

```js
module.exports = {
   // ...
    module: {
        rules: [
            {
                test: /\.(png|jpg|jpeg|gif)$/,   // 匹配以png|jpg|jpeg|gif结尾的文件
                loader: 'url-loader',
              	options: {
                  limit: 10 * 1024, // 小于10kb会用base64编码处理
                  name: '[hash:10].[ext]', // 打包后的文件命名为hash命名，设置为取hash的前10位
                  esModule: false, // 关闭es模块化，使用commonJs模块化
                  outputPath: 'imgs'
                }
            }
        ]
    }
}
```

### 压缩html

配置

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  // ...
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html',  //  以此目录下的index.html文件为模板
      minify: {
        collapseWhitespace: true, // 去除空格
        removeComments: true // 去除注释
      }
    })
  ]
}
```

### 其他文件的处理

```js
module.exports = {
   // ...
    module: {
        rules: [
            {
                test: /\.(font|...)$/,   // 匹配以font|...结尾的文件
              	exclude: /\.(css|less|png|jpg|jpeg|gif|html)$/, // 排除以 css|less|png|jpg|jpeg|gif|html结尾的文件
                loader: 'file-loader'
              	options: {
              			outputPath: 'media' // 保存到单个文件夹中
            		}
            }
        ]
    }
}
```

### 优化

1. oneOf

正常来讲，一个文件要被所有 loader 过一遍，最终会被满足匹配规则的 loader 处理

配置：

```js
module.exports = {
    rules: [
      {
            test: /\.js$/,
            exclude: /node_module/,
            loader: 'eslint-loader',
          	enforce: 'pre',    // 优先执行
            options: {
                fix: true // 代码自动改正
            }
        },
      	{
          // 一下文件只会被 loader 处理
          // 如果有一种类型的文件被多个 loader 处理，需要提取到外部，还要注意处理顺序
          oneOf: [
            {
              test: /\.js$/,
              exclude: /node_module/,
              loader: 'babel-loader',
              options: {
                presets: [
                  [
                    '@babel/preser-env',
                    {
                      useBuildIns: 'usage', // 使用corejs
                      corejs: { version: 3}， // corejs的版本
                      target: { // 对哪些浏览器的版本生效
                      chrome: '60', // chrome 60 版本及以上
                      firefox: '50' // firefox 50 版本及以上
                    }
                    }
                  ]
                ]
              }
            }
          ]
        }
        
    ]
};
```

2. 缓存

- babel缓存：不需要每次转换时都加载babel
- 文件资源缓存
  - hash：每次webpack构建时会生成一个唯一的hash值
  - chunkhash：根据chunk生成的hash值。如果打包来源于同一个chunk，那么hash值就相同
  - contenthash：根据文件内容生成hash值，不同文件hash值一定不一样 --> 类似于协商缓存

配置

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'built.[contenthash:10].js',
    path: path.resolve(__dirname, 'build')
  },
	module: {
    rules: [
      	{
        		test: /\.css$/,
          	use: [
              	MiniCssExtractPlugin.loader,
              	'css-loader'
            ]
        },
        {
            test: /\.js$/,
            exclude: /node_module/,
            loader: 'babel-loader',
            options: {
                presets: [
                  [
                  	'@babel/preser-env',
                    {
                      useBuildIns: 'usage', // 使用corejs
                      corejs: { version: 3}， // corejs的版本
                      target: { // 对哪些浏览器的版本生效
                        chrome: '60', // chrome 60 版本及以上
                        firefox: '50' // firefox 50 版本及以上
                      }
                    }
                  ]
                ]
            }
        }
    ]
  },
  
  plugins: [
    new MiniCssExtractPlugin({
      filename: 'css/built.[contenthash:10].css',
    })
  ],
  
  mode: 'production'
};
```

3. Tree-shaking

去除在应用程序中没有使用的代码，让代码体积更小

前提：1. 使用ES6模块化  2.使用production

4. 代码分割：code split

将打包的一个chunk分割为多个文件，可以实现按需加载

- 第一种方式：单入口/多入口

```js
const path = require('path');

module.exports = {
  entry: {
    // 一般是多页应用
    index: './src/index.js',
    sub: './src/sub.js'
  },
  output: {
    filename: 'js/[name].js',
    path: path.resolve(__dirname, 'build')
  },
  // ...
}
```

- 第二种方式：optimization:
  - 可以将node_module中代码单独打包一个chunk并输出
  - 自动分析多入口chunk中有没有公共文件，如果有会打包成单独一个chunk

```js
module.exports = {
  // ...
  optimization: {
    splitChunks: {
      chunks: 'all'
    }
  }
}
```

- 第三种方式：通过js代码，让某个文件单独打包成一个chunk。需要掌握import动态导入语法

```js
import(/* webpackChunkName：test */'./test') // 默认会用id命名，这种写法会用 test 命名
	.then(res => {
		console.log(res)
		// do something...
	}).catch(err => {
		console.log(err);
  	// do something...
	})
```

4. 懒加载与预加载：js文件的懒加载

使用import动态导入语法

```js
// 懒加载：当文件使用时才加载
// 预加载 prefetch：会在使用之前，提前加载js文件

// 正常加载是并行加载（同一时间加载多个文件）；预加载是等其他资源加载完毕，浏览起空闲了，再偷偷加载资源

// 以下是某个按钮的异步方法
document.getElementById('btn').onclick = function () {
  
  import(/* webpackChunkName：test, webpackPrefetch: true */'./test')
    .then(res => {
      console.log(res)
      // do something...
    }).catch(err => {
      console.log(err);
      // do something...
    })
}
```

5. PWA(Progressive Web App)：渐进式网页应用

核心模块为service worker和web push，让web像app一样能离线访问，性能也更好

使用：

​	workbox： workbox-webpack-plugin

```js
const WorkboxWebpackPlugin = require('workbox-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  // ...
  plugins: [
    new WorkboxWebpackPlugin.generateSW({
      /*
      	1. 帮助service worker快速启动
      	2. 删除旧的service worker
      	最终生成service worker的配置文件
      */
      clientClaim: true,
      skipWaiting: true
    })
  ]
}

// 注册 service worker
// 1. 处理 service worker 兼容性问题
// ./src/index.js
if ('serviceWorker' in navigator) {
  window.serviceworker(() => {
    navigator.register('./service-worker.js')
      .then(() => {
      console.log('cw注册成功'),
    }).catch(err => {
      console.log(err);
    }
  })
}
// 2. 处理eslint不认识 window navigator全局变量
// ./src/index.js
{
    // ...
    "eslintConfig": {
        "extends": 'airbnb-base', // 继承 airbnb-base 风格的规则
        "env": {
            "browser": true
        }
    }
}
// 3. service worker的代码必须运行在服务器上
// --> npm i serve -g
// --> serve -s build 启动服务器，将build目录下所有资源作为静态资源暴露出去
```

6. 多进程打包（有利有弊，启动进程和进程通信也需要耗费时间）

安装：

```powershell
npm i thread-loader -D
```

配置：一般用于babel

```js
module.exports = {
  module:{
    rules: [
        {
            test: /\.js$/,
            exclude: /node_module/,
          	use: [
              'thread-loader',
              {
                loader: 'babel-loader',
                options: {
                    presets: [
                      [
                        '@babel/preser-env',
                        {
                          useBuildIns: 'usage', // 使用corejs
                          corejs: { version: 3}， // corejs的版本
                          target: { // 对哪些浏览器的版本生效
                            chrome: '60', // chrome 60 版本及以上
                            firefox: '50' // firefox 50 版本及以上
                          }
                        }
                      ]
                    ]
                }
              }
            ]
          
        }
    ]
  }
};
```

7. externals

防止将某些方法输出到最终打包的bundle中

配置

```js
module.exports = {
  //...
  mode: 'production',
  externals: {
    // 拒绝包（后面的是 npm 包名）被打包进来
    jquery: 'jQuery'
  }
}
```

8. dll 动态连接库


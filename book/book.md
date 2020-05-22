### 1. 导读
#### 1.1 知识点
- Loader
- HMR
- Create React App
- Caching
- Plugin
- SourceMap
- Vue Cli 3.0
- Shimming
- WebpackDevServer
- TreeShaking
- CodeSplitting
- Babel
- React
- Library
- Eslint
- PWA
- Vue
- Mode
- 性能优化
- 多页应用
- 原理
- PreLoading
- PreFetching
- 环境变量
- TypeScript

#### 1.2 收获
- 彻底学会Webpack的配置
- 理解Webpack的作用及原理
- 上手项目的打包过程配置
- 拥有工程化的前端思维
- 步入高级前端工程师行列

### 2. 初始Webpack
#### 2.1 Webpack 究竟是什么
- index.js
  ``` javascript
  import Header from './header.js'
  import Sidebar from './sidebar.js'
  import Content from './content.js'

  new Header()
  new Sidebar()
  new Content()
  ```
- header.js
  ``` javascript
  function Header() {
    var dom = document.getElementById('root')
    var header = document.createElement('div')
    header.innerText = 'header'
    dom.append(header)
  }

  export default Header
  ```
- sidebar.js
  ``` javascript
  function Sidebar() {
    var dom = document.getElementById('root')
    var sidebar = document.createElement('div')
    sidebar.innerText = 'sidebar'
    dom.append(sidebar)
  }

  export default Sidebar
  ```
- content.js
  ``` javascript
  function Content() {
    var dom = document.getElementById('root')
    var content = document.createElement('div')
    content.innerText = 'content'
    dom.append(content)
  }

  export default Content
  ```

- 安装webpack-cli
  ``` shell
  npm install webpack-cli --save-dev
  npm install webpack --save
  npx webpack index.js
  ```
- index.html
  ``` html
  <html lang="en">
    <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>这是最原始的网页开发</title>
    </head>
    <body>
      <p>这是我们的网页内容</p>
      <div id="root"></div>
      <script src="./dist/bundle.js"></script>
    </body>
  </html>
  ```

#### 2.2 什么是模块打包工具？
模块规范
- ES Module 模块引入方式
- CommonJS 模块引入规范
- CMD
- AMD

#### 2.3 Webpack的正确安装方式
- 安装Node.js
- npm init
- package.json
  ``` json
  {
    "private": true
  }
  ```
- npm install webpack webpack-cli -g (不推荐)
- webpack -v
- npm uninstall webpack webpack-cli -g (卸载)
- webpack -v
- npm install webpack webpack-cli -D
- webpack -v
- npx webpack -v
- npm info webpack (查看版本号)
- npm install webpack@4.16.5 webpack-cli -D
- webpack -v
- npx webpack --config webpackconfig.js

#### 2.4 使用Webpack的配置文件
- webpack.config.js
  ``` javascript
  const path = require('path')

  module.exports = {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  }
  ```
- package.json
``` json
{
  "name": "webpack4_learn",
  "version": "1.0.0",
  "description": "",
  "private": true,
  "main": "index.js",
  "scripts": {
    "bundle": "webpack",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.43.0",
    "webpack-cli": "^3.3.11"
  },
  "dependencies": {
    "webpack": "^4.43.0"
  }
}
```

#### 2.5 浅析Webpack打包输出内容
- Hash: 本次打包对应的唯一hash值
- Version: webpack版本
- Time: 打包耗时
- Built at: 打包时的时间
- Asset: 打包出的文件
- Size: 包的大小
- Chunks: 打包出的js文件id值
- Chunk Names: 打包出的js文件名字(entry中配置的main)
- mode: production(压缩) or development(不压缩)

- webpack.config.js
  ``` javascript
  const path = require('path')

  module.exports = {
    mode: 'development',
    entry: {
      main: './src/index.js'
    },
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  }
  ```

### 3. Webpack 核心概念
#### 3.1 Loader 是什么
- 安装file-loader
``` shell
cnpm install file-loader -D
```
- index.js
  ``` javascript
  import Avatar from './1.jpg'
  var img = new Image()
  img.src = Avatar

  var root = document.getElementById('root')
  root.append(img)
  ```
- webpack.config.js
  ``` javascript
  const path = require('path')

  module.exports = {
    mode: 'development',
    entry: {
      main: './src/index.js'
    },
    module: {
      rules: [{
        test: /\.jpg$/,
        use: {
          loader: 'file-loader'
        }
      }]
    },
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  }
  ```

#### 3.2 使用 Loader 打包静态资源（图片篇）
- 打包出的图片名不变
  webpack.config.js
  ``` javascript
  const path = require('path')

  module.exports = {
    mode: 'development',
    entry: {
      main: './src/index.js'
    },
    module: {
      rules: [{
        test: /\.(jpg|png|gif)$/,
        use: {
          loader: 'file-loader',
          options: {
            // placeholder 占位符
            name: '[name]_[hash].[ext]',
            outputPath: 'images/'
          }
        }
      }]
    },
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  }
  ```
- url-loader

  ``` shell
  cnpm install url-loader -D
  ```
  大于limit的值，生成images文件夹，小于limit的值，生产base64的值
  webpack.config.js
  ``` javascript
  const path = require('path')

  module.exports = {
    mode: 'development',
    entry: {
      main: './src/index.js'
    },
    module: {
      rules: [{
        test: /\.(jpg|png|gif)$/,
        use: {
          loader: 'url-loader',
          options: {
            // placeholder 占位符
            name: '[name]_[hash].[ext]',
            outputPath: 'images/',
            limit: 2048
          }
        }
      }]
    },
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  }
  ```

#### 3.3 使用 Loader 打包静态资源（图片篇）
#### 3.3 使用 Loader 打包静态资源（图片篇）

### 4. Webpack 进阶
### 5. Webpack 实战配置案例
### 6. Webpack 底层原理及脚手架工具分析
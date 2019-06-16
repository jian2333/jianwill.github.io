---
title: 初识 webpack
date: 2019-06-16 21:22:50
top:
categories:
- 学习笔记
tags:
- web前端
- webpack
- js
---

webpack 是一个现代 JavaScript 应用程序的静态模块打包器。

当使用 webpack 打包时，webpack 会从一个或多个入口处，递归地构建一个 `依赖关系图(dependency graph)` ，其中包含应用程序所需要的所有模块，然后打包成一个或多个 `bundle` 文件。

webpack 可以打包 `js`、`css`、`sass`、`less` 、`typescript`、`图片` 等文件，默认只能打包 `js` 格式的文件，其他格式的文件需要加载 `loader` 后再打包。

编写本文的时候，webpack 的最新版为 `v4.34.0` 。

<!--more-->

### 概念

webpack 可使用 `终端/命令行` 来执行，当然，webpack 还是 **高度可配置的** 。

webpack 有以下四个 **核心概念** ：

- 入口(entry)
- 输出(output)
- loader
- 插件(plugins)

**入口(entry)**

- webpack 打包时的入口；

- 可以有一个或多个入口；

- 入口会依赖其他文件，webpack 会以此为依据，进而找到其他依赖的文件和关系，然后一起打包成一个或多个 `bundle.js` 文件；

  ```js
  module.exports = {
    entry: './app/main.js'
  };
  ```

**出口(output)**

- 用于描述打包后的 `bundle.js` 文件的存放路径和命名规则，默认值为 `./dist` ；

  ```js
  const path = require('path');
  module.exports = {
    entry: './app/main.js',
    output: {
      path: path.resolve(__dirname, 'public'),
      // 等效于 path: __dirname + '/public',
      filename: 'bundle.js'
    }
  };
  
  // __dirname 是node.js的一个全局变量，表示当前执行脚本所在的目录
  ```

**loader**

- `loader` 能够让 webpack 打包那些 `非javascript` 文件；

- `loader` 需要单独安装并在 `webpack.config.js` 中的 `modules` 关键字下进行配置，`loader` 的配置包括以下几个方面：

  - `test` ：一个用以匹配 `loader` 所处理文件的扩展名的正则表达式(必须) ；
  - ~~`loader`~~ `use` ：`loader` 的名称(必须) ；
  - ~~`include/exclude` ：手动添加必须处理的文件(文件夹) 或屏蔽不需处理的文件(文件夹) ；~~
  - ~~`query` ：为 `loader` 提供额外的处理选项 ；~~

- [更多信息](https://www.webpackjs.com/concepts/loaders/) ；

  ```js
  module.exports = {
    ...
    module: {
      rules: [
        { test: /\.css/, use: 'css-loader' },
        { test: /\.ts/, use: 'ts-loader' }
      ]
    }
  };
  ```

**插件(plugins)**

- `loader` 被用来转换某些类型的模块，而插件则用来执行范围更广的任务；

- 插件的范围包括：打包、优化、压缩、重新定义环境中的变量等其他功能；

- 插件的使用和 `loader` 类似，先使用 `npm` 安装，然后在 `webpack.config.js` 中的 `plugins` 关键字下添加该插件的一个实例( `plugins` 是一个数组 )。

- [更多信息](https://www.webpackjs.com/concepts/plugins/) ；

  ```js
  module.exports = {
    ...
    module: {
      rules: [
        { test:/\.css/, use: 'css-loader' },
        { test:/\.ts/, use: 'ts-loader' }
      ]
    },
    plugins: [
      new webpack.optimize.UglifyJsPlugin(),
      new HtmlWebpackPlugin({ template: './src/index.html' })
    ]
  };
  ```

### 其他要点

**webpack 可配置的**

- 可使用 `终端/命令行` 来执行 webpack，也可配置好 `webpack.config.js`，然后直接使用 `webpack` 命令；

- 全局安装的使用 `webpack` 命令，非全局安装的使用 `node_modules/.bin/webpack` 命令；

- 这个命令会自动引用 `webpack.config.js` 文件中的配置选项；

- 如果没有 `webpack.config.js` 配置文件，也可使用下面的命令来打包；

  ```bash
  // 未配置 webpack.config.js
  // npx webpack {入口} -o {出口}
  npx webpack app/main.js -o public/bundle.js
  
  // 已配置 webpack.config.js，未配置 package.json
  npx webpack --config webpack.config.js
  npx webpack
  webpack
  
  // 已配置 webpack.config.js，已配置 package.json
  npm start
  ```

  

**script 脚本命令**

- 命令配置在 `package.json` 中的 `scripts` 关键字中；

- `start` 是 特殊的脚本命令，使用 `npm start` 即可，其他脚本 `xx` ，则需使用 `npm run xx` 来执行，比如 `npm run build` ；

  ```js
  {
    "name": "webpackdemo1",
    "version": "1.0.0",
    "description": "webpack sample project",
    "private": true,
    "main": "index.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "start": "webpack",
      "server": "webpack-dev-server --open"
    },
    "author": "",
    "license": "ISC",
    "devDependencies": {
      "webpack": "^4.34.0",
      "webpack-cli": "^3.3.4",
      "webpack-dev-server": "^3.7.1"
    }
  }
  ```

  ![](/images/wp-1.jpg)

**生成 Source Maps （使调试更容易）**

- `source maps` 提供了一种对应编译文件和源文件的方法，使编译后的代码可读性更高，更容易调试；

- 在 `webpack.config.js` 中的 `devtool` 关键字添加 `devtool` 选项即可，一般使用 `source-map` ；

  ```js
  module.exports = {
    devtool: "source-map",
    entry: __dirname + '/app/main.js',  // 已多次提到的唯一入口文件
    output: {
      path: __dirname + '/public',    // 打包后文件存放的路径
      filename: 'bundle.js'   // 打包后输出的文件名称
    },
  };
  ```

- [更多信息](https://www.webpackjs.com/configuration/devtool/) ；

**webpack 构建本地服务器（支持 模块热替换）**

- webpack 可构建本地服务器，然后可配置一系列相关的设置；

- 本地服务器支持 **模块热替换** 功能，即修改 JS 代码后，浏览器会自动刷新页面；

- 使用方法：

  - 安装 `webpack-dev-server` 组件；

    ```js
    npm install --save-dev webpack-decv-server
    ```

  - 在 `webpack.config.js` 中的 `devServer` 关键字中进行配置；

    ```js
    module.exports = {
      ...
      devServer: {
        contentBase: './public',	// 本地服务器所加载的页面所在的目录
        historyApiFallback: true,	// 不跳转
        inline: true	// 实时刷新
      }
    }
    ```

  - 在 `package.json` 中的 `scripts` 中添加对应的命令( `server` 命令 )；

    ```js
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "start": "webpack",
      "server": "webpack-dev-server --open"
    }
    ```

  - 在 `终端/命令行` 输入 `npm run server` 即可启动服务器；

    ![](/images/wp-2.jpg)

- 更多信息：

  - [使用webpack构建本地服务器](https://segmentfault.com/a/1190000006178770#articleHeader3)
  - [使用 webpack-dev-server](https://www.webpackjs.com/guides/development/#使用-webpack-dev-server)

### 实例

**新建项目**

- ```bash
  mkdir webpackdemo
  cd webpackdemo
  npm init
  ```

**安装 webpack 和 webpack-cli**

- ```bash
  npm install webpack webpack-cli --save-dev
  ```

**配置 webpack**

- 新建 `app` 文件夹，用来存放 `js` 源文件；

- 新建 `public` 文件夹，用来存放 `html` 文件和打包生成的  `bundle.js` 文件；

- 当前项目结构如下：

  ![](/images/wp-3.jpg)

- 配置 webpack 的 入口和出口，如下：

  ```js
  // webpack.config.js
  module.exports = {
      devtool: "source-map",
      entry: __dirname + '/app/main.js',  // 已多次提到的唯一入口文件
      output: {
          path: __dirname + '/public',    // 打包后文件存放的路径
          filename: 'bundle.js'   // 打包后输出的文件名称
      },
      devServer: {
          contentBase: './public',    // 本地服务器所加载的页面所在的目录
          historyApiFallback: false,   // 不跳转
          inline: true    // 实时刷新
      }
  };
  ```

**配置 script 命令**

- ```js
  // package.json
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack",
    "server": "webpack-dev-server --open"
  },
  ```

**编写代码**

- 在 `/app` 文件夹中添加 `Greeter.js` 和 `main.js` 文件；

- 在 `/public` 文件夹中添加 `index.html` 文件；

- 文件内容分别如下：

  ```js
  // Greetet.js
  module.exports = function () {
      var greet = document.createElement('div');
      greet.textContent = "Hi there are greetings!";
      return greet;
  };
  ```

  ```js
  // main.js 入口文件
  const greeter = require('./Greeter');
  document.querySelector('#root').appendChild(greeter());
  ```

  ```html
  <!-- index.html -->
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Webpack Sample Project</title>
  </head>
  <body>
      <div id="root"></div>
      <script src="bundle.js"></script>
  </body>
  </html>
  ```

**运行**

- 打包生成 `bundle.js` 文件；

  ```bash
  npm start
  ```

- 启动服务器；

  ```bash
  npm run server
  ```

### 推荐文章

[1. [推荐]官方文档](https://www.webpackjs.com/concepts/)

[2. [版本较旧]入门Webpack，看这篇就够了](https://segmentfault.com/a/1190000006178770#articleHeader5)
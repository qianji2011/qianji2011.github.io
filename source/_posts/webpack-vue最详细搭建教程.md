---
title: webpack+vue最详细搭建教程
date: 2022-01-28 23:20:47
tags: webpack
categories: 技术
---

### 基础环境
- 使用的最新版本webpack5+，要求安装[nodejs](https://nodejs.org/en/download/),最低版本是 10.13.0 (LTS)
- 我们将使用npm指令进行操作，最好使用[nrm](https://www.npmjs.com/package/nrm)将npm镜像源切换到淘宝镜像

### 安装步骤
- 初始化项目
创建一个目录，并在该目录下进行初始化npm

``` bash
PS D:\webFiles\project> mkdir webpack-test-demo


    目录: D:\webFiles\project


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         2022/1/29     22:05                webpack-test-demo


PS D:\webFiles\project> cd .\webpack-test-demo\
PS D:\webFiles\project\webpack-test-demo> npm init -y
Wrote to D:\webFiles\project\webpack-test-demo\package.json:

{
  "name": "webpack-test-demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```
此时修改package.json

``` bash
{
  "name": "webpack-test-demo",
  "version": "1.0.0",
  "description": "",
  "private": true,
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```
- 安装webpack并配置
webpack4+版本，将webpack-cli分离出来。所以这个时候需要同时安装webpack, webpack-cli

``` bash
PS D:\webFiles\project\webpack-test-demo> npm install webpack webpack-cli -D
npm notice created a lockfile as package-lock.json. You should commit this file.
+ webpack-cli@4.9.2
+ webpack@5.67.0
added 120 packages from 157 contributors in 21.092s
```
然后按照如下目录结构创建文件
![目录结构](https://i.bmp.ovh/imgs/2022/01/43e1dddf6390d66b.png)

其中，public\template.html中代码如下
``` bash
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">Hello, Webpack!</div>
    <script src="../dist/bundle.js"></script>
</body>
</html>
```
src\index.js中代码如下
``` bash
console.log('Hello, webpack!');
```
webpack.config.js中代码如下
``` bash
const path = require('path')

const webpackConfig = {
    entry: path.join(__dirname, 'src/index.js'),
    output: {
        filename: 'bundle.js',
        path: path.join(__dirname, 'dist')
    }
}

module.exports = webpackConfig
```
修改package.json修改如下
``` bash
{
  "name": "webpack-test-demo",
  "version": "1.0.0",
  "description": "",
  "private": true,
  "scripts": {
    "build": "webpack"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^5.67.0",
    "webpack-cli": "^4.9.2"
  }
}
```
然后在终端执行如下命令
``` bash
PS D:\webFiles\project\webpack-test-demo> npm run build

> webpack-test-demo@1.0.0 build D:\webFiles\project\webpack-test-demo
> webpack

asset bundle.js 31 bytes [emitted] [minimized] (name: main)
./src/index.js 31 bytes [built] [code generated]

WARNING in configuration
The 'mode' option has not been set, webpack will fallback to 'production' for this value.
Set 'mode' option to 'development' or 'production' to enable defaults for each environment.
You can also set it to 'none' to disable any default behavior. Learn more: https://webpack.js.org/configuration/mode/

webpack 5.67.0 compiled with 1 warning in 981 ms
```
此时的目录结构如下，已将生成了压缩后的bundle.js
![生成bundle.js](https://i.bmp.ovh/imgs/2022/01/8ff43d7c256e6a08.png)
在浏览器中打开 public\template.html，设置的信息按预期展示
![初步页面](https://i.bmp.ovh/imgs/2022/01/65b0f2699ddd4585.png)

- 安装webpack-dev-server,html-webpack-plugin
虽然已经能够进行打包，但是文件修改一次就要进行手动执行打包命令，实在是很麻烦。所以需要安装webpack-dev-server，用来监听文件修改，实时编译打包，并应用html-webpack-plugin生成打包后的html

首先执行以下命令安装
```bash
PS D:\webFiles\project\webpack-test-demo> npm install webpack-dev-server -D
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@~2.3.2 (node_modules\chokidar\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@2.3.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})
npm WARN ws@8.4.2 requires a peer of bufferutil@^4.0.1 but none is installed. You must install peer dependencies yourself.
npm WARN ws@8.4.2 requires a peer of utf-8-validate@^5.0.2 but none is installed. You must install peer dependencies yourself.
npm WARN ajv-formats@2.1.1 requires a peer of ajv@^8.0.0 but none is installed. You must install peer dependencies yourself.

+ webpack-dev-server@4.7.3
added 217 packages from 176 contributors in 36.922s
PS D:\webFiles\project\webpack-test-demo> npm install html-webpack-plugin -D
npm WARN ajv-formats@2.1.1 requires a peer of ajv@^8.0.0 but none is installed. You must install peer dependencies yourself.
npm WARN ws@8.4.2 requires a peer of bufferutil@^4.0.1 but none is installed. You must install peer dependencies yourself.
npm WARN ws@8.4.2 requires a peer of utf-8-validate@^5.0.2 but none is installed. You must install peer dependencies yourself.
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@2.3.2 (node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@2.3.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

+ html-webpack-plugin@5.5.0
added 30 packages from 16 contributors in 10.514s
```
接着在webpack.config.js中增加配置
``` bash
const HtmlWebpackPlugin = require('html-webpack-plugin')

const webpackConfig = {
    // 其他设置
    plugins: [
        new HtmlWebpackPlugin({
            template: path.join(__dirname, 'public/template.html'),
            filename: 'index.html'
        })
    ],
    mode: 'development'
}
```
将public\template.html中如下代码删除，并将dist目录删除
``` bash
 <script src="../dist/bundle.js"></script>
```
接着在package.json的scripts中增加如下属性
``` bash
    "dev": "webpack-dev-server --open --port 8080 --hot"
```
然后在终端执行如下命令
``` bash 
PS D:\webFiles\project\webpack-test-demo> npm run dev

> webpack-test-demo@1.0.0 dev D:\webFiles\project\webpack-test-demo
> webpack-dev-server --open --port 8080 --hot

<i> [webpack-dev-server] Project is running at:
<i> [webpack-dev-server] Loopback: http://localhost:8080/
<i> [webpack-dev-server] On Your Network (IPv4): http://192.168.43.74:8080/
<i> [webpack-dev-server] On Your Network (IPv6): http://[fe80::343b:c76f:1841:520f]:8080/
<i> [webpack-dev-server] Content not from webpack is served from 'D:\webFiles\project\webpack-test-demo\public' directory
asset bundle.js 243 KiB [emitted] (name: main)
asset index.html 348 bytes [emitted]
runtime modules 27 KiB 12 modules
modules by path ./node_modules/ 161 KiB
  modules by path ./node_modules/webpack-dev-server/client/ 56.8 KiB 12 modules
  modules by path ./node_modules/webpack/hot/*.js 4.3 KiB
    ./node_modules/webpack/hot/dev-server.js 1.59 KiB [built] [code generated]
    ./node_modules/webpack/hot/log.js 1.34 KiB [built] [code generated]
    + 2 modules
  modules by path ./node_modules/html-entities/lib/*.js 81.3 KiB
    ./node_modules/html-entities/lib/index.js 7.74 KiB [built] [code generated]
    ./node_modules/html-entities/lib/named-references.js 72.7 KiB [built] [code generated]
    ./node_modules/html-entities/lib/numeric-unicode-map.js 339 bytes [built] [code generated]
    ./node_modules/html-entities/lib/surrogate-pairs.js 537 bytes [built] [code generated]
  ./node_modules/ansi-html-community/index.js 4.16 KiB [built] [code generated]
  ./node_modules/events/events.js 14.5 KiB [built] [code generated]
./src/index.js 31 bytes [built] [code generated]
webpack 5.67.0 compiled successfully in 2504 ms
```
此时浏览器自动打开了页面
![自动打开页面](https://i.bmp.ovh/imgs/2022/01/06455c27ffafd505.png)
- 安装babel
为了让ES5+的语法能在当前及旧版本的浏览器或其他环境中运行，需要安装babel-loader等相关包对js文件进行编译
``` bash
PS D:\webFiles\project\webpack-test-demo> npm install babel-loader @babel/core @babel/preset-env @babel/plugin-transform-runtime -D
npm WARN ajv-formats@2.1.1 requires a peer of ajv@^8.0.0 but none is installed. You must install peer dependencies yourself.
npm WARN ws@8.4.2 requires a peer of bufferutil@^4.0.1 but none is installed. You must install peer dependencies yourself.
npm WARN ws@8.4.2 requires a peer of utf-8-validate@^5.0.2 but none is installed. You must install peer dependencies yourself.
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@2.3.2 (node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@2.3.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

+ @babel/core@7.16.12
+ @babel/plugin-transform-runtime@7.16.10
+ babel-loader@8.2.3
+ @babel/preset-env@7.16.11
added 161 packages from 66 contributors in 25.798s
```
然后在webpack.config.js中增加配置
``` bash
const webpackConfig = {
  // 省略其他配置
    module: {
        rules: [
            {
                test: /\.js$/i,
                exclude: /node_modules/,
                use:['babel-loder']
            }
        ]
    },
```
接着在webpack.config.js的同级目录下新增.babelrc文件，该文件的配置如下
``` bash
{
    "presets": [
        "@babel/preset-env"
    ],
    "plugins": [
        "@babel/plugin-transform-runtime"
    ]
}
```
- 安装css-loader
在前端代码里面，基本的样式文件css，要使webpack支持对这种文件的编译，需要安装css-loader，style-loader。解析less及sass文件所需要的loader不在这里阐述，需要的可以在[官网](https://webpack.docschina.org/loaders/)进行查看
``` bash
PS D:\webFiles\project\webpack-test-demo> npm install css-loader style-loader -D
npm WARN ajv-formats@2.1.1 requires a peer of ajv@^8.0.0 but none is installed. You must install peer dependencies yourself.
npm WARN ws@8.4.2 requires a peer of bufferutil@^4.0.1 but none is installed. You must install peer dependencies yourself.
npm WARN ws@8.4.2 requires a peer of utf-8-validate@^5.0.2 but none is installed. You must install peer dependencies yourself.
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@2.3.2 (node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@2.3.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

+ css-loader@6.5.1
+ style-loader@3.3.1
added 16 packages from 47 contributors in 6.773s
```
接着在package.json种增加配置
``` bash
const webpackConfig = {
   // 省略其他配置
    module: {
        rules: [
            {
                test: /\.css$/i,
                use: ['style-loader','css-loader']
            }
        ]
    }
}
```
然后在src目录下创建styles目录，并在styles目录下创建common.css文件，该css文件中代码如下
``` bash
body {
    background-color: yellow;
}
```
然后在src\index.js引入common.css
``` bash
import './styles/common.css'
// 省略其他代码
```
接着执行以下命令 npm run dev
``` bash
PS D:\webFiles\project\webpack-test-demo> npm run dev

> webpack-test-demo@1.0.0 dev D:\webFiles\project\webpack-test-demo
> webpack-dev-server --open --port 8080 --hot

<i> [webpack-dev-server] Project is running at:
<i> [webpack-dev-server] Loopback: http://localhost:8080/
<i> [webpack-dev-server] Content not from webpack is served from 'D:\webFiles\project\webpack-test-demo\public' directory
<i> [webpack-dev-middleware] wait until bundle finished: /
asset bundle.js 265 KiB [emitted] (name: main)
asset index.html 348 bytes [emitted]
runtime modules 27 KiB 12 modules
modules by path ./node_modules/ 169 KiB
  modules by path ./node_modules/webpack-dev-server/client/ 56.8 KiB 12 modules
  modules by path ./node_modules/style-loader/dist/runtime/*.js 5.75 KiB 6 modules
  modules by path ./node_modules/webpack/hot/*.js 4.3 KiB 4 modules
  modules by path ./node_modules/html-entities/lib/*.js 81.3 KiB 4 modules
  modules by path ./node_modules/css-loader/dist/runtime/*.js 2.33 KiB
    ./node_modules/css-loader/dist/runtime/noSourceMaps.js 64 bytes [built] [code generated]
    ./node_modules/css-loader/dist/runtime/api.js 2.26 KiB [built] [code generated]
  ./node_modules/ansi-html-community/index.js 4.16 KiB [built] [code generated]
  ./node_modules/events/events.js 14.5 KiB [built] [code generated]
modules by path ./src/ 2.8 KiB
  ./src/index.js 61 bytes [built] [code generated]
  ./src/styles/common.css 2.27 KiB [built] [code generated]
  ./node_modules/css-loader/dist/cjs.js!./src/styles/common.css 476 bytes [built] [code generated]
webpack 5.67.0 compiled successfully in 4753 ms
```
自动打开浏览器后看到common.css的背景颜色设置生效了
![样式文件解析](https://i.bmp.ovh/imgs/2022/01/2442bca24d43a87d.png)
- 安装vue
此时安装的是vue2，参照[官网](https://cn.vuejs.org/v2/guide/installation.html)的说明，使用npm install vue进行安装
``` bash
PS D:\webFiles\project\webpack-test-demo> npm install vue
npm WARN ajv-formats@2.1.1 requires a peer of ajv@^8.0.0 but none is installed. You must install peer dependencies yourself.
npm WARN ws@8.4.2 requires a peer of bufferutil@^4.0.1 but none is installed. You must install peer dependencies yourself.
npm WARN ws@8.4.2 requires a peer of utf-8-validate@^5.0.2 but none is installed. You must install peer dependencies yourself.
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@2.3.2 (node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@2.3.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

+ vue@2.6.14
added 1 package from 1 contributor in 7.392s
```
然后在src\index.js文件中增加如下代码
``` bash
import Vue from 'vue'
// 省略其他代码
new Vue({
    el: "#app",
    data: {
        msg: 'it is a text from vue'
    }
})
```
并修改public\template.html中的代码为：
``` bash
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">Hello, Webpack!{{msg}}</div>
</body>
</html>
```
接着执行npm run dev，终端虽然执行成功，没有报错，但是浏览器控制台有报错
![控制台报错](https://i.bmp.ovh/imgs/2022/01/7b38a4b96ab4b3d7.png)
这个报错的意思是，需要安装模板编译器，那就缺啥补啥
``` bash
PS D:\webFiles\project\webpack-test-demo> npm install vue-loader vue-template-compiler -D
npm WARN ajv-formats@2.1.1 requires a peer of ajv@^8.0.0 but none is installed. You must install peer dependencies yourself.
npm WARN ws@8.4.2 requires a peer of bufferutil@^4.0.1 but none is installed. You must install peer dependencies yourself.
npm WARN ws@8.4.2 requires a peer of utf-8-validate@^5.0.2 but none is installed. You must install peer dependencies yourself.
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@2.3.2 (node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@2.3.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

+ vue-template-compiler@2.6.14
+ vue-loader@15.9.8
added 17 packages from 9 contributors in 9.582s
```
接着在webpack.config.js中增加配置
``` bash
const {VueLoaderPlugin} = require('vue-loader')

const webpackConfig = {
    // 省略其他代码
      module: {
        rules: [
            {
                test: /\.vue$/i,
                use: ['vue-loader']
            }
        ]
    },
    plugins: [
        new VueLoaderPlugin()
    ],
    resolve: {
    alias: {
      'vue$': 'vue/dist/vue.esm.js' // 用 webpack 1 时需用 'vue/dist/vue.common.js'
      }
    }
}
```
接着执行npm run dev，在打开的浏览器中已经正常显示了信息
![成功显示](https://i.bmp.ovh/imgs/2022/01/8cefb3f02e2b6fe1.png)
vue最常使用的vue单文件，其实已经可以正常解析了。此时在webpack.config.js同级目录下创建app.vue，该文件中的代码如下
``` bash
<template>
    <div class="main">{{childMsg}}</div>
</template>
<script>
export default {
    name: 'app',
    data(){
        return {
            childMsg: 'it is from child'
        }
    }
}
</script>
```
并修改src\index.js中代码为
``` bash
import './styles/common.css'
import Vue from 'vue'
import app from '../app.vue'

new Vue({
    el: "#app",
    data: {
        msg: 'it is a text from vue'
    },
    render(h){
        return h(app)
    }
})
```
![加载vue文件](https://i.bmp.ovh/imgs/2022/01/468ca295a113a2ba.png)


到这里就完成了webpack+vue项目的基础搭建
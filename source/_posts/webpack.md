---
title: webpack
date: 2017-04-02 15:01:04
categories: 构建工具
tags: [webpack]
toc: true
---

webpack说的很简单，项目中也一直在用别人搭好的脚手架，一直很忙没有功夫细细研究下，这次一定认真研究下，以后使用再有问题和坑一并记录到这来

> 参考api http://webpack.github.io/docs/configuration.html
> 中文api https://doc.webpack-china.org/guides/get-started/
> 简易使用指南 https://github.com/petehunt/webpack-howto


<!-- more -->

为什么用webpack？目标是什么？

将依赖切分到不同的代码块里面，按需加载依赖
保持初始化加载更少
整合第三方模块

区别

- code splitting 代码分隔

- loaders

- 模块热更新

- 插件系统


当你拿到一个项目你需要

# 初始化项目

```
npm init
```

一路回车生成package.json

接下来你需要全局安装webpack

```
npm i -g webpack
```

在项目中安装

```
npm i -D webpack
```

创建如下目录结构

```
+ src
  - app.js
+ dist
package.json

```

就可以使用webpack 进行简单的打包

app.js

```
console.log('hello from app.js')

```

可以使用webpack进行简单打包

```
webpack ./src/app.js ./dist/app.bundle.js

```


```
webpack ./src/app.js ./dist/app.bundle.js -p //打包出来的文件被压缩处理

```


```
webpack ./src/app.js ./dist/app.bundle.js --watch //打包出来的文件被压缩处理,并且实时监听文件的变化

```
可以看到打包好的文件生成在dist文件中，现在我们简化指令

创建webpack.config.js

```
module.exports = {
  entry: './src/app.js',
  output: {
    filename: './dist/app.bundle.js'
  }
}
```
再输入

```
webpack
```

修改package.json

```
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "dev": "webpack -d --watch"  //这条是添加的
}
```

这时我们在终端只需输入

```
npm run dev
```

而实际上线我们需要代码压缩

```
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "dev": "webpack -d --watch",
  "prod": "webpack -p"  // 对代码进行压缩
},
```

# webpack plugins 创建自定义的index

在dist目录中创建index.html,并进入打包好的js文件

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <p>content goes here</p>
  <script src="./app.bundle.js"></script>
</body>
</html>
```

此刻我们不想通过手动在dist中创建html文件，利用webpack的plugin来帮我们创建


```
npm i html-webpack-plugin --save-dev
```
修改webpack.config.js,并删除dist目录中的index.html

```
var HtmlWebpackPlugin = require('html-webpack-plugin');
var path = require('path')
module.exports = {
  entry: './src/app.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'app.bundle.js'
  },
  plugins: [new HtmlWebpackPlugin()]
}
```

然后运行

```
npm run dev
```

这时候你会看见目录里面自动生成index.html文件

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Webpack App</title>
  </head>
  <body>
  <script type="text/javascript" src="app.bundle.js"></script></body>
</html>
```

如果想根据自己的模板创建index.html,需要修改webpack.config.js

```
var HtmlWebpackPlugin = require('html-webpack-plugin');
var path = require('path')
module.exports = {
  entry: './src/app.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'app.bundle.js'
  },
  plugins: [
    new HtmlWebpackPlugin({
      title: 'myApp',
      minify: {
        collapseWhitespace: true //生成被压缩的html文件
      },
      hash: true,
      template: './src/index.html', // 自定义的html路径
    })
  ]
}
```

在src目录添加index.html

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title><%= htmlWebpackPlugin.options.title %></title>
</head>
<body>
  <p>This is my template</p>
</body>
</html>
```

# css-load、sass-load

## css-load

```
npm install css-loader style-loader --save-dev
```

在app目录下生成app.css

```css
html,body{
  height:100%;
  margin:0;
  background:green;
  color:#fff;
  font-size:20px;
}
```

修改webpack.config.js

```
module: {
    rules: [
      {test: /\.css$/, loaders: 'style-loader!css-loader'}
      // {test: /\.css$/, use: ['style-loader', 'css-loader']}
    ]
  },
```

修改app.js

```
import './app.css'
console.log('hello from app.js again')

```
 
 ## sass-loader

```
npm install --save-dev sass-loader node-sass

```

修改webpack.config.js

```
module: {
    rules: [
      {test: /\.css$/, use: ['style-loader', 'css-loader']},
      {test: /\.scss$/, use: ['style-loader', 'css-loader', 'sass-loader']}
    ]
  },
```

修改app.js

```
import './main.scss'
console.log('hello from app.js again')

```

在src目录新建main.scss

```
body{background:#ff0;}
```

这样的打包方式最终加载页面head里面，但是如果吓你文件的方式引入进去

```
npm install --save-dev extract-text-webpack-plugin

```

修改webpack.config.js

```
var HtmlWebpackPlugin = require('html-webpack-plugin');
const ExtractTextPlugin = require("extract-text-webpack-plugin");
var path = require('path')
module.exports = {
  entry: './src/app.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'app.bundle.js'
  },
  module: {
    rules: [
      {test: /\.css$/, use: ExtractTextPlugin.extract({
        fallback: 'style-loader',
        use: ['css-loader']
      })},
      {test: /\.scss$/, use: ExtractTextPlugin.extract({
        fallback: 'style-loader',
        use: ['css-loader', 'sass-loader']
      })}
    ]
  },
  plugins: [
    new ExtractTextPlugin({
      filename:  (getPath) => {
        return getPath('css/[name].css').replace('css/js', 'css');
      },
      allChunks: true
    }),
    new HtmlWebpackPlugin({
      title: 'myApp',
      minify: {
        collapseWhitespace: true //生成被压缩的html文件
      },
      hash: true,
      template: './src/index.html', // Load a custom template (ejs by default see the FAQ for details)
    })
  ]
}
```

# webpack-dev-server

```
npm i webpack-dev-server -D

```

修改package.json

```
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "dev": "webpack-dev-server", // 这里是刚修改的
  "prod": "webpack -p"
}

```
然后

```
npm run dev

```

修改webpack.config.js，添加如下

```
devServer: {
  contentBase: path.join(__dirname, 'dist'),
  compress: true,
  port: 8080,
  stats: 'errors-only',
  open: true // 启动后自动打开浏览器窗口
},
```

# 清理dist

```
npm i -D rimraf

```

package.json修改

```
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "dev": "webpack-dev-server",
  "prod": "npm run clean && webpack -p",
  "clean": "rimraf ./dist/*"
}
```

这样的话会在每次打包的时候将dist目录清空，然后重新生成，以确保dist目录没有多余的无用文件

# 多模块的使用

一般情况一个项目肯定不止一个页面吧，解析来创建contact模块，修改webpack.config.js,在plugins中添加：

```
new HtmlWebpackPlugin({
  title: 'contact',
  hash: true,
  filename: 'contact.html',
  template: './src/contact.html'
})
```

在src根目录创建一个新的html模板contact.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title><%= htmlWebpackPlugin.options.title %></title>
</head>
<body>
  <p>This is contact!</p>
</body>
</html>
```

新建contact.js

```js

console.log('contact page')
```

由于有多个入口文件，修改webpack.config.js的entry和output

```
entry: {
  app: './src/app.js',
  contact: './src/contact.js'
},
output: {
  path: path.resolve(__dirname, 'dist'),
  filename: '[name].bundle.js'
},
```

这时候再次启动服务器，我们发现contact.js被调用，这不是我们需要的，指向在contact.js在contact页面中被调用

```js
new HtmlWebpackPlugin({
  title: 'myApp',
  hash: true,
  filename: './index.html',
  excludeChunks: ['contact'], //新增
  template: './src/index.html',
}),
new HtmlWebpackPlugin({
  title: 'contact',
  hash: true,
  filename: 'contact.html',
  chunks: ['contact'], //新增
  template: './src/contact.html'
})
```

这样两个模块就不会互相干扰

# css js的局部刷新

伴随项目越来越大，每次保存都会从新刷新打包，现在我们需要局部刷新

修改webpack.config.js

```
var HtmlWebpackPlugin = require('html-webpack-plugin');
const ExtractTextPlugin = require("extract-text-webpack-plugin");
var path = require('path')
var webpack = require('webpack')
module.exports = {
  entry: {
    app: './src/app.js',
    contact: './src/contact.js'
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].bundle.js'
  },
  module: {
    rules: [
      {test: /\.css$/, use: ['style-loader', 'css-loader']},
      {test: /\.scss$/, use: ['style-loader', 'css-loader', 'sass-loader']},
      {test: /\.pug$/, use: ['html-loader', 'pug-html-loader']}
    ]
  },
  devServer: {
    contentBase: path.join(__dirname, 'dist'),
    compress: true,
    port: 8080,
    stats: 'errors-only',
    hot: true,
    open: true // 启动后自动打开浏览器窗口
  },
  plugins: [
    new ExtractTextPlugin({
      filename:  (getPath) => {
        return getPath('css/[name].css').replace('css/js', 'css');
      },
      disable: true,
      allChunks: true
    }),
    new HtmlWebpackPlugin({
      title: 'myApp',
      // minify: {
      //   collapseWhitespace: true //生成被压缩的html文件
      // },
      hash: true,
      filename: './index.html',
      excludeChunks: ['contact'],
      template: './src/index.pug', // Load a custom template (ejs by default see the FAQ for details)
    }),
    new HtmlWebpackPlugin({
      title: 'contact',
      hash: true,
      filename: 'contact.html',
      chunks: ['contact'],
      template: './src/contact.html'
    }),
    new webpack.HotModuleReplacementPlugin(),
    new webpack.NamedModulesPlugin()
  ]
}
```

# 打包图片

```
npm i -D file-loader
```

```
// add 
{test: /\.jpg$/, use: 'file-loader'}

``` 

在html中增加使用

```
<img src="<%= require('./img/bg.jpg')%>" alt="">

```

```
npm i -D image-webpack-loader

```

```
{
  test: /\.(png|jpe?g|svg|gif|webp)$/,
  use: [
    'file-loader?name=images/[name].[ext]',
    // 'file-loader?name=[hash:6].[ext]&publicPath=images/',
    'image-webpack-loader?{optimizationLevel: 7, interlaced: false, pngquant:{quality: "65-90", speed: 4}, mozjpeg: {quality: 65}}'
  ]
}
```

> 具体参考：https://www.npmjs.com/package/image-webpack-loader

# babel的使用

```
npm i -D babel babel-preset-es2015 babel-loader babel-core

```

```
//add
{
  test: /\.js$/,
  use: 'babel-loader',
  exclude: /node_modules/
},
```
在根目录新增.babelrc文件

```
{
  "presets": ["es2015"]
}

```
> 具体参考 http://babeljs.io/docs/setup/#installation

> webpack 详解 http://www.viscode.cn/2017/03/30/webpack%E8%AF%A6%E8%A7%A3/#more

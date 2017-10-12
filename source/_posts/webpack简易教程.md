---
title: webpack简易教程
date: 2017-06-26 23:32:04
categories: 构建工具
tags: [webpack]
toc: true
---

> 项目在mac上如果报错 

运行 brew install libpng

参考了 https://github.com/tcoopman/image-webpack-loader/issues/51 

> 项目地址  https://github.com/mayufo/webpack-practice
> webpack  https://webpack.github.io/
> npm  http://www.npmjs.com/
> 之前写的webpack文章 https://mayufo.github.io/2017/04/02/webpack/


<!-- more -->


# chapter 1

webpack hello.js hello.bundle.js


npm install css-loader style-loader --save-dev

css-loader 处理生成css文件
style-loader 是加载

如果不想在require写

可以在命令行敲

webpack hello.js hello.bundle.js --module-bind 'css=style-loader!css-loader'


webpack hello.js hello.bundle.js --module-bind 'css=style-loader!css-loader' --watch 随时监听

webpack hello.js hello.bundle.js --module-bind 'css=style-loader!css-loader' --progress 看到打包过程

webpack hello.js hello.bundle.js --module-bind 'css=style-loader!css-loader'  --progress --display-modules 展示打包的模块

webpack hello.js hello.bundle.js --module-bind 'css=style-loader!css-loader'  --progress --display-modules --display-reasons 可以看到打包这个模块的原因


# chapter 2 


> http://webpack.github.io/docs/configuration.html

改名字 为 webpack.dev.config.js

webpack --config webpack.dev.config.js

## entry

平行的不互相依赖

```
entyl: ['./entry1', 'entry2']
```
## output

多页面应用

```
{
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/build'
  }
}
```

[name] 打包的名字

[hash] 打包的版本号

[chunkhash] 每个文件的版本，相当于md5值

打包的情况

```js
output: {
        path: './dist',
        filename: 'js/[name]-[chunkhash].js',
        publicPath: 'http://cdn.com' //上线地址
   }
```


# chapter 3 生成index

npm install html-webpack-plugin --save-dev


> http://webpack.github.io/docs/using-plugins.html

可以遍历出里面的参数

```eje
<%= htmlWebpackPlugin.options.date %>
<% for (var key in htmlWebpackPlugin.files) {%>
<%= key %> : <%= JSON.stringify(htmlWebpackPlugin.files[key])%>
<% } %>

<% for (var key in htmlWebpackPlugin.options) {%>
<%= key %> : <%= JSON.stringify(htmlWebpackPlugin.options[key])%>
<% } %>
```

> 参考插件的api https://www.npmjs.com/package/html-webpack-plugin

打出的内容
```
Thu Jun 22 2017 11:52:57 GMT+0800 (中国标准时间)

publicPath : ""

chunks : {"main":{"size":24,"entry":"js/main-6409dbec7a578634313c.js","hash":"6409dbec7a578634313c","css":[]},"a":{"size":46,"entry":"js/a-ac3cfa9c31ab31c7ee2e.js","hash":"ac3cfa9c31ab31c7ee2e","css":[]}}

js : ["js/main-6409dbec7a578634313c.js","js/a-ac3cfa9c31ab31c7ee2e.js"]

css : []

manifest : 



template : "D:\\study\\webpack-practice\\node_modules\\html-webpack-plugin\\lib\\loader.js!D:\\study\\webpack-practice\\index.html"

filename : "index.html"

hash : false

inject : "head"

compile : true

favicon : false

minify : false

cache : true

showErrors : true

chunks : "all"

excludeChunks : []

title : "webpack is good"

xhtml : false

date : "2017-06-22T03:52:57.284Z"
```
> 一部分js放在头部，一部分放在body

通过设置

```html
<script src="<%= htmlWebpackPlugin.files.chunks.a.entry %>"></script>
```

压缩html

> https://www.npmjs.com/package/html-webpack-plugin 

minify 里面设置 可以看html-minifier 来设置

## 多页面的应用

可以用chunks：[] 以数组的方式来表现

注意在多页面应用的时候如果template的index.html不能有多余的备注

如果一个page中需要多个js,可以用excludeChunks

## 提高请求，将js inline在page中

```
npm install --save-dev html-webpack-inline-source-plugin
```

```js
plugins: [
  new HtmlWebpackPlugin({
		inlineSource: '.(js|css)$' // embed all javascript and css inline
	}),
  new HtmlWebpackInlineSourcePlugin()
]  
```

这时候inject: false

> 具体可以参考 https://github.com/DustinJackson/html-webpack-inline-source-plugin

也可以用

```
<%=
            compilation.assets[htmlWebpackPlugin.files.chunks.main.entry.substr(htmlWebpackPlugin.files.publicPath.length)].source() %>
```

> 将main inline在html里面 ， 将对应的js 分别加入

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title><%= htmlWebpackPlugin.options.title %></title>
    <script type="text/javascript">
        <%=
            compilation.assets[htmlWebpackPlugin.files.chunks.main.entry.substr(htmlWebpackPlugin.files.publicPath.length)].source() %>
    </script>

</head>

<body>
    <% for (var k in htmlWebpackPlugin.files.chunks) { %>
        <% if(k!== 'main') { %>
            <script type="text/javascript" src="<%= htmlWebpackPlugin.files.chunks[k].entry%>"></script>
        <% } %>
    <% } %>

<!--我是注释-->
</body>
</html>
```

```js
var htmlWebpackPlugin = require('html-webpack-plugin');

// var HtmlWebpackInlineSourcePlugin = require('html-webpack-inline-source-plugin');

module.exports = {
    // context: '', // 运行环境的上下文
    // entry: ['./src/script/main.js', './src/script/a.js'],
    entry: {
        main: './src/script/main.js',
        a: './src/script/a.js',
        b: './src/script/b.js',
        c: './src/script/c.js'
    },
    output: {
        path: './dist',
        filename: 'js/[name]-[chunkhash].js',
        publicPath: 'http://cdn.com' //上线地址
    },

    plugins: [
        new htmlWebpackPlugin({
            template: 'index.html',
            // filename: 'index-[hash].html',
            filename: 'main.html',
            inject: false,  // 也可以放在body标签里面
            title: 'webpack is good',
            date: new Date(),
            minify: {
                removeComments: true, // 删除注释
                collapseWhitespace: true  // 删除多余的空格
            },
            // chunks: ['main', 'a'],
            inlineSource: '.(js|css)$'
        }),
        new htmlWebpackPlugin({
            template: 'index.html',
            // filename: 'index-[hash].html',
            filename: 'a.html',
            inject: false,  // 也可以放在body标签里面
            title: 'a',
            // chunks: ['a']
            // excludeChunks:  ['b','c'],
            inlineSource: '.(js|css)$'
        }),
        new htmlWebpackPlugin({
            template: 'index.html',
            // filename: 'index-[hash].html',
            filename: 'b.html',
            inject: false,  // 也可以放在body标签里面
            title: 'b',
            // chunks: ['b']
            // excludeChunks: ['a','c'],
            inlineSource: '.(js|css)$'
        }),
        new htmlWebpackPlugin({
            template: 'index.html',
            // filename: 'index-[hash].html',
            filename: 'c.html',
            inject: false,  // 也可以放在body标签里面
            title: 'c',
            // chunks: ['c'],
            // excludeChunks: ['a','b'],
            inlineSource: '.(js|css)$'

        }),
        // new HtmlWebpackInlineSourcePlugin()
    ]

}

```

# chapter 4 如何使用loader


> http://webpack.github.io/docs/list-of-loaders.html#packaging

## 使用
> http://webpack.github.io/docs/using-loaders.html

- 在require声明中使用
```js
require("url-loader?mimetype=image/png!./file.png");
```

- 配置config文件

```js
{
    test: /\.png$/,
    loader: "url-loader",
    query: { mimetype: "image/png" }
}
```

- CLI中

```
webpack --module-bind "png=url-loader?mimetype=image/png"
```

## 如何用ES6

> using Babel http://babeljs.io/docs/setup/#installation

```
npm install --save-dev babel-loader babel-core
```

因为es6不断变化，所以用的配置文件不一样，通过制定presets

> http://babeljs.io/docs/plugins/#transform-plugins


选择 latest 

```
npm install --save-dev babel-preset-latest
```

在config中配置

也可以另外建立个.babelrc文件

```js
{
    "presets": ["latest"]
}
```
也可以在package.json里面定义

```
{
  "name": "webpack-practice",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "bable": {
    "presets": ["latest"] // 增加的
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack --config webpack.config.js --progress --display-modules --colors --display-reasons"
  },
```

> 打包的速度

babel 非常耗时

test: A condition that must be met
exclude: A condition that must not be met  排除的
include: An array of paths or files where the imported files will be transformed by the loader 包含的
loader: A string of “!” separated loaders
loaders: An array of loaders as string


## 绝对路径到相对路径

引入path

path.resolve 是解析路径

```js
loaders: [
          {
              test: /\.js$/,
              loaders: ['babel'],
              exclude: path.resolve(__dirname, 'node_modules'), 
          }
      ]
```

## css-loader

```
npm install css-loader style-loader --save-dev
```

```
{
              test: /\.css$/,
              loaders: ['style-loader', 'css-loader']
          }
```
### 给css加前缀

```
npm i  postcss-loader  --save-dev  // 给css做预处理
npm install autoprefixer --save-dev // 给css加前缀
```

> autoprefixerApi https://github.com/postcss/autoprefixer
> postcss plugins https://github.com/postcss/postcss

另外添加postcss.config.js
```
module.exports = {

    plugins: [
        require('autoprefixer')({
            broswers: ['last 5 versions']
        })
    ]
};
```

### css中存在import的语法

引入进来的也需要postcss-loader

```
loaders: ['style-loader', 'css-loader?importLoaders=1', 'postcss-loader'],
```
执行顺序从右向左

在css-loader之后用1个loaders来处理import进来的资源


## less-loader

```
npm install --save-dev less-loader less
```

```
{
     test:  /\.less$/,
     loaders: ['style-loader', 'css-loader', 'postcss-loader', 'less-loader']

}
```

当less中有import的时候，如果css中已经有import的配置，less就不需要配置了

## sass-loader

```
npm install sass-loader node-sass webpack --save-dev
```

```js
{
              test:  /\.less$/,
              loaders: ['style-loader', 'css-loader', 'postcss-loader', 'less-loader']

          }
```

## 处理模板文件

> webpack 参考文档 http://webpack.github.io/docs/list-of-loaders.html#templating
> git项目的文档 https://github.com/webpack-contrib/html-loader

```
npm i html-loader --save-dev
```

```
npm i ejs-load --save-dev
```


```
 loaders: [
          {
              test: /\.js$/,
              loaders: ['babel'],
              exclude: path.resolve(__dirname, 'node_modules'),
              // include: path.resolve(__dirname, 'src')
          },
          {
              test: /\.css$/,
              loaders: ['style-loader', 'css-loader?importLoaders=1', 'postcss-loader'],
          },
          {
              test:  /\.less$/,
              loaders: ['style-loader', 'css-loader', 'postcss-loader', 'less-loader']

          },
          {
              test:  /\.scss/,
              loaders: ['style-loader', 'css-loader', 'postcss-loader', 'sass-loader']

          },
          {
              test:  /\.html$/,
              loaders: ['html-loader']

          },
          {
              test: /\.ejs$/,
              loaders: ['ejs-loader']
          }
      ]
```

## 处理图片文件

```
npm install file-loader --save-dev
```

> https://github.com/webpack-contrib/file-loader

```js
{
  test: /\.(png|jpg|gif|svg)$/i,
  loaders: ['file-loader']
}
```

如果是在项目上引用相对路径的图片,写在模板index.html

如果在组件中插入图片，路径用的相对地址，打包的时候地址没有替换

地址可以写成

```
<img src="${require('../../assets/img.jpg')}" alt="">
```


如果希望修改图片生成的地址

```
{
  test: /\.(png|jpg|gif|svg)$/i,
  loaders: ['file-loader?name=asset/[name]-[hash:5].[ext]']
}
```

```
{
  test: /\.(png|jpg|gif|svg)$/i,
  loader: 'file-loader',
  query: {
      name: 'asset/[name]-[hash:5].[ext]'
  }
}
```

## url-loader

处理文件图片大小大于制定的limit 扔给file-loader, 当小于这个lime,它可以转成一个64位编码
```
npm install url-loader --save-dev
```

```
{
  test: /\.(png|jpg|gif|svg)$/i,
  loader: 'url-loader',
  query: {
      limit: 20000,
      name: 'asset/[name]-[hash:5].[ext]'
  }
}
```

http 在如果来的图片会有缓存，如果图片重复性很高用加载

base64 没有缓存，容易导致代码的冗余和增加代码的体积

## image-loader 压缩图片

```
npm install image-webpack-loader --save-dev
```

> image-loader Api https://github.com/tcoopman/image-webpack-loader

对每个格式的图片都有优化器，可以通过参数设置





---
title: vue学习
date: 2017-07-22 14:38:49
categories: vue
tags: [vue,study，miaov]
toc: true
---

# vue介绍

学习记录

<!-- more -->

## 渐进式框架Vue

> api  https://cn.vuejs.org/v2/guide/

构建用户界面的**渐进式框架**

声明式渲染-组件系统-客户端路由-大规模状态管理-构建工具

按需加载功能，不用全部加载

只关注view层

## 两个核心点

- 响应的数据绑定

 数据发生改变 -> 自动更新视图

利用`Object.definedProperty`中的`setter/getter`代理数据，监控对数据的操作，可以在控制台中方便打出`app.__vue__.message`，不兼容IE8

- 可组合的视图组件

ui 页面映射为组件树
划分组件可维护、可重用、可测试

## 虚拟DOM

js运行速度很快，dom操作速度很慢

![](https://github.com/mayufo/mayufo.github.io/blob/master/2017/07/22/vue%E5%AD%A6%E4%B9%A0/vue_1.png)

将html模板或者字符串模板编译，调用Render函数，将模板里面的函数抽离出来，形成虚拟dom树，其实就是对象嵌套对象映射到dom,生成真正的dom树

![](https://github.com/mayufo/mayufo.github.io/blob/master/2017/07/22/vue%E5%AD%A6%E4%B9%A0/vue_2.png)

一个地方改变，只渲染改变的地方，而不用渲染全部

## MVVM模式

M: model 数据模型
V：view 视图模型
VM: view-model 视图模型 数据的绑定和事件的监听

![](https://github.com/mayufo/mayufo.github.io/blob/master/2017/07/22/vue%E5%AD%A6%E4%B9%A0/vue_3.png)

## Vue实例

### vue实例

```vue
new Vue(选项对象)
```
`el`挂载元素选择器
`data` 代理数据
`methods`放置事件处理函数

### vue代理data数据

每个Vue实例都会代理器data对象里所有的属性，这些被代理的属性是响应的，新添加的属性不具备响应功能，改变后不会更新视图

### vue实例自身属性和方法
app是template的id值
```vue
app.__vue__
```

### 声明式渲染

采用简洁的模板语法来声明式的将数据渲染进DOM

```js
var arr = [1,2,3,4,5];
var newArr = arr.map(function(item) {
  return item * 2;
})
```

### 命令式代码

每一步都要实现,考虑到

```js
var arr = [1,2,3,4,5];
var newArr = [];
for(var i = 0,len = arr.length; i < len; i++) {
  newArr.push(arr[i]*2);
}
```

## 指令

`v-bind` 动态的绑定数据，简写：
`v-on` 绑定事件监听器，简写@
`v-text` 更新数据，会覆盖已有的结构
`v-html` 可以解析数据中的html结构
`v-show` 根据值得真假，切换元素的display
`v-if` 根据值得真假，切换元素会被销毁重建
`v-else-if` 多条件判断，为真则渲染
`v-else` 条件都不符合渲染
`v-for` 基于源数据多次渲染元素或者模板
`v-model` 在表单控件元素上创建双向数据绑定
`v-pre` 跳过元素和子元素的编译过程
`v-once` 只渲染一次，随后数据更新不重新渲染
`v-cloak` 保持在元素上直到关联实例结束编译，css中设置[v-cloak]{display: none}
```html
<div v-cloak>
  {{ message }}
</div>
```

## html模板
基于DOM的模板，模板都是可解析的有效HTML

插值

- 文本 使用`Mustache`语法(双大括号) {{value}},
当改变为自动更新

- html 双大括号输出是文本，不会解析html,可以用`v-html`
span里面不能包含div
```html
  <div v-html="message"></div>
```

- 属性
使用v-bind来绑定
```html
<div :attr="message"></div>
```

- 使用js表达式,不能写语句
```html
<div>{{1 + 2}}</div>   
```

## 字符串模板

template选项对象的属性
模板会替换挂载的元素，挂载元素的内容将被胡烈
template模板根节点只能有一个,多个根节点会警告
用字符串拼接的时候，可以用``实现
把html片段放在script里面，用代码片段,无法做到复用

```js
new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue.js!'
  },
	template:'<div>33333333333333333</div>'
})
```
http://runjs.cn/code/7tahwicq

使用template片段

```html
<script src="https://unpkg.com/vue"></script>
<script type="x-template" id="temp">
  <div>111</div>
  <span>{{message}}</span>
</script>
<div id="app">
  <p>{{ message }}</p>
</div>
```
```js
new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue.js!'
  },
	template:'#temp'
})
```

http://runjs.cn/code/ybceq6re

## 模板render函数
```js
new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue.js!'
  },
	render(createElement) {
		return createElement(
			"ul",
			[
					createElement('li', 1),
					createElement('li', 2),
					createElement('li', 3)
			]
	)}
})
```
http://runjs.cn/code/b8yxestz

最接近编译器，但是想给ul上加一些属性,一般用在组件上

`createElement(标签名，[数据对象]，子元素)`

- 数据对象属性

```js
new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue.js!'
  },
	render(createElement) {
		return createElement(
	"ul",{ class: { bg: true}, style:{fontSize: "18px"}, attrs: {abc: "miaov"},domProps: {innerHTML: "<li>may</li>"}},  // 显示innerHTML
			
			[
					createElement('li', 1),
					createElement('li', 2),
					createElement('li', 3)
			]
	)}
})
```

`class： {}` 绑定class
`style: {}` 绑定样式
`attrs: {}` 添加行间属性
`domProps:{}`DOM元素属性
`on: {}` 绑定事件

`nativeOn: {}` 监听原生事件
`directives: {}` 自定义指令
`scopedSlots: {}` slot作用域
`slot: {}` 定义slot名称
`key: "key"` 给元素添加唯一表示
`ref: "ref"` 引用信息




# 路由

不同的url对应不同的应用程序，通过管理url,实现对组件的切换

单页应用（SPA）

优点：一次性加载，适合手机端


- 安装模块

```
npm install vue-router --save
```

- 引入模块

import VueRouter from 'vue-router'

- 作为Vue的插件

Vue.use(VueRouter)

- 创建路由实例对象

```
new VueRouter({
    ...配置参数
})
```

- 注入Vue选项参数

new Vue({
    router
})

- 告诉路由渲染的位置
```html
<router-view></router-view>
```

# 路由的两种模式
- hash 模式`#` ,需要给每个页面对应的路径前加`#`,默认的模式
```html
<li><a href="#/home">home</a></li>
```
- 希望在路由中`#`号消失，用`history`模式,在路由中配置
```js
let router = new VueRouter({
  mode: 'history',
  routes: [
    {
      path: '/',
      component: Home
    },
    {
      path: '/about',
      component: About
    },
    {
      path: '/document',
      component: Document
    }
  ]
})
```
其他应用的路由前面不用加`#`号， 但是在`a`标签中跳转链接，会影响单页面应用
```html
<router-link to="/home">home</router-link>
```
使用`router-link`组件，不用手动配置 ，推荐使用`history`模式

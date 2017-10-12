---
title: react 慕课网入门笔记
date: 2017-03-15 10:47:27
categories: react
tags: [react,study]
toc: true
---
学习慕课网的react 入门教程 

> 视频地址 http://www.imooc.com/learn/504

> API http://reactjs.cn/react/docs/getting-started-zh-CN.html

<!-- more -->

# React 介绍

 react 不是完整的mvc框架，mvvm框架
 
 用组件的方式，通过组合来实现大的模块
 
 应用场景
 
 - 复杂场景的高性能
 
 - 组件高度可重用
 
JSX = JS + XML

语法糖，计算机语言中没有添加的某种语法，对功能没有影响，增加可阅读性，减少出错。例如 JSX 最终都会编译成JS

react 里面的 <div> 不能看成 dom ,而是react component的实例

# React 的 style


> demo http://jsfiddle.net/reactjs/69z2wepo/

# React-components-lifecycle

Mounted -> Update -> Unmounted

- Mounted

React Components被render解析生成对应的dom节点并被插入浏览器的DOM解构的一个过程,当在浏览器从无到有的渲染的一个过程，就是Mounted结束，我们就说这个component组件已经被Mounted

- Update

一个mounted的react Components 被重新render的过程,而这个重新被渲染的过程并不是说dom解构会发生改变，react 会把这个component的当前state 和最近一次state进行对比，只有发现确实发生的改变并且影响的当前的DOM结构
，react 才会改变对应的dom解构

- Unmounted

一个Mounted的React Components 对应的DOM节点被从DOM解构中移除的一个过程

## hook() 函数

每一个状态react都封装了对应的hook函数，设计思想是`will`和`did`, 将要做什么了和已经做了什么。

- Mounted  

getDefaultProps() -> getInitialState() -> componentWillMount() -> render -> componentDidMount()

   - componentWillMount() 将要Mounted调用
   - componentDIdMount() 已经Mounted调用
   - getInitialState() 初始化react state的
   
   props和state都可以设置css样式
   state值是可变的，props是通过组件调用方，通过组件决定，一旦定义不改变
   
   
   this 指调用函数的对象
   当前this compoentent 的实例
   如果在setTimeout里面this指代globl对象
   如果在构造函数中，this就指这个新生成的对象
   通过调用apply call bind 调用后的this对象    
   
   > demo http://codepen.io/mayufo/pen/EWQmKN
   
   state 值每次变化，会进入updating 状态，使其进入一个render的过程
   
- updating

componentWillReceiveProps -> shouldComponentUpdate -> componentWillUpdate -> render -> componentDidUpdate
    
    - componentWillReceiveProps() Mounted Component将要接收新的props时，这个函数会被调用，其函数参数就是新的props对象，可以通过参数和this.props进行比较，来执行一些例如修改state的操作
    
    - shouldComponentUpdate 当接收到新的props和state之后，判断是否有必要去更新dom结构，有两个参数，一个是新的props对象，另一个是新的state对象，可以分别对比，来决定是否更新dom,返回true是更新dom节点，返回false是不更新dom节点。
    
- Unmounting   
    
   - componentWillUnmount() 可以释放内存资源，图片资源 
    
# react component
    
   state的更新，会触发页面更新，而传统的页面更新时通过修改页面dom实现
   
   传统的事件处理通过 event listener，
   
   > demo http://codepen.io/mayufo/pen/GWQvxM
   
   
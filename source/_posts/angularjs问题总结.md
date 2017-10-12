---
title: angularjs问题总结
date: 2017-04-01 23:18:47
categories: angular
tags: [angular, 问题，面试]
toc: true
---

之前在知乎上看过一个问题  

> 如何衡量一个人的 AngularJS 水平？ https://www.zhihu.com/question/36040694

感觉自己也用了快一年了，但是很多会用答不上来，吓得我赶紧打开我的项目，发现我用angularjs啦，难道我遇见假的angularjs了。而且用完就忘了，开此贴特来记录一些问题和坑！

<!-- more -->

## angular的数据采用什么机制？ 详述原理

脏检测机制

angular在scope模型上设置了一个监听队列，用来监听数据变化并更新view。当绑定一个东西到view上时angular就会往$watch队列里插入一条$watch,用来检测它监听的model里面是否有变化的东西。当浏览器接收可以被angular content 处理的事件是，$digest循环就会触发，遍历所有$watch, 最后更新dom

举个例子

```html
<button ng-click = 'val=val+1'></button>
```

click 时会产生一次更新的操作（至少触发两次$digest循环）
- 按下按钮
- 浏览器接收一个事件，进入到angular context
- $digest循环开始执行，查询每个$watch是否变化
- 由于监听$scope.val的$watch报告了变化，因此强制再执行一次$digest循环
- 新的$digest循环没有检测到变化
- 浏览器拿回控制器，更新$scope.val新值对应的dom


$digest的循环上限是10次


## 两个平级界面a和b,如果a触发一个时间，有哪些方式能让b知道，详述原理

其实就是两个评级界面模块进行通信。有两种方式

- 共用服务

- 基于事件

- eventBus 事件发布和订阅的框架

### 共用服务

在angular中，通过factory可以生成一个单例对象，在与需要通信的模块a和b中注入这个对象即可

### 基于事件

- 借助父controller.在子controller中向父controller触发`$emit`事件，然后在父controller中监听`$on`事件，再广播`$broadcast`给子controller,这样通过事件携带参数，实现了数据经过父controller,在同级controller之间传播

- 借助$rootScope。每个angular应该默认一个根作用域`$rootScope`,根作用域位于最顶层，从它往下挂着各种作用域。所以，如果子控制器直接使用`$rootScope`广播和接收事件，那么久可以实现同级之间的通信

### EventBus 事件发布、订阅总线

提供事件订阅，事件发布，事件解绑

将定义的事件和事件函数push到一个数组里面这个是事件订阅，当需要触发函数通过事件发布，完成后事件解绑

## 一个angular应用应当如何良好的分层

- 目录结构的划分

如果是小型的文件类型，可以按照文件类型组织

css
js
  controller
  model
  services
  filters
  templates

但如果是规模较大的项目，最好按照业务模块划分

业务文件夹
最好还有个公共的common文件来存放公共的东西，比如抽出的组件，图片，css

- 逻辑代码的划分

作为一个mvvm框架，angular应用本身应该按照模型，视图模型，视图来划分

这里的逻辑代码的拆分，尽量让controller这一层很薄。提取共用的逻辑到service中，比如后台数据的请求，数据的共享和缓存，基于事件的模块间通信，提取共同的界面操作到directive中，提取共用的格式化造作到filter中

在复杂的应用中，也可以为实体建立对应的构造函数

## angular应用常用那些路由库，各自的区别

angular1.x中常用ngRoute 和 ui.router,还有一种为angular2设计的new router

ngRouter 是angular自带的angular模块，ui.router是第三方提供的模块

ui.router是基于state状态的，ngRouter是基于url的，ui.router模块具有强大的功能，主要体现在视图的嵌套方面

使用ui.router能够定义有明确父子关系的路由，并通过ui-view指令将子路由模板插入到父路由模板的<div ui-view></div>中去，从而实现视图嵌套。而在ngRouter中不能这样定义，如果同时在父子视图中使用的<div ng-view></div>会陷入死循环

## 如果angular的directive规划一套全组件化体系，会遇到哪些挑战？

- 对外暴露的接口是否满足不断变化的需求
- 能否做到一套组件化的细节例如disabled 默认值 css等细节的统一
- 能否做到统一的版本控制，当升级组件版本时，基础逻辑不会影响，做到兼容
- 及时fix bug


## 分属不同团队进行开发的angular应用，如果要做整合，会遇到哪些问题，如何解决

可能会遇到模块之间的冲突，一个在moduleA中，一个在moduleB中，导致两个module在会发生覆盖

最好在开发的时候就统一空间命名，避免冲突


## angular缺点有哪些？

- 约束性强学习成本高，对前端不友好

- 不利于seo, 一种解决的办法是对应正常用户的访问，响应angular,对于搜索引擎访问，响应针对seo的html页面

- 性能问题

作为MVVM框架，因为实现了数据的双向绑定，对应大数组，复杂对象会存在性能问题

## 优化angular应用性能的办法

- 减少监控项 对不会变化的使用单向绑定 
```html
{{::yourModel}}

```

- 主动设置索引 指定track by， 无限滚动加载避免用`ng-repeat`


- 降低渲染数量 设置分页，每次渲染有限的数量

- 数据扁平化 (对于树状结构，使用扁平化解构，构建一个map和梳妆数据，对数操作)

## 如何看待 controller as 的语法

在1.2以前，view任何的绑定都是直接绑定$scope。使用controllerAs,不需要再注入$scope, controller 变成一个简单的js对象，一个更纯粹的viewModel

从源码实现上来看，controllerAs语法只是把controller这个对象的实例用as别名在$scope上创建一个属性

避免遇到angular作用域相关的一个坑（比如ng-if，会产生以及作用域的坑，其实也是js原型链继承中值类型继承的坑）使用controllerAs的话view上所有的字符都绑定在一个引用的属性上，就可以避免这个坑


不过不引入`$scope`会导致`$emit $broadcast $on $watch`等方法无法使用，这些个跟事件相关的操作可以封装起来统一处理，或者在单个controller中引入 $scope


## 详述angular的依赖注入

angular是通过构造函数的参数名字来推断依赖服务名称，通过toString来找到这个定义的function的字符串，然后用正则解析出其中的参数，再去依赖映射种渠道的对应的依赖，实例化之后传入

因为angular的inject是假设函数的参数名字就是依赖名字，如果去查找依赖项，代码压缩后，参数会重命名，就无法查找到依赖项


依赖注入写法
数组注释法
```js
myApp.controller('myCtrl', ['$scope', function ($scope) {

}])
```

显示$inject
```js
myCtrl.$inject = ['$scope', '$http']

```

对于依赖注入必须具备三个要素：依赖项的注册，依赖关系的声明和对象的获取，在angularjs中，module和$provide都可以提供依赖项的注入，内置的injector可以获取对象

### ng-if和ng-show区别

ng-if 为true 才创建dom节点，ng-show起始就创建了。用样式控制

ng-if 会隐式的产生新作用域，ng-switch，ng-include等会动态创建一块界面也是如此


### ng-repeat 迭代数组，有相同值

可以加`$track by $index`可以解决，也可以任何一个普通的值，只要能唯一标识数组中的每一项即可（建立dom和数据之间的关系）

## ng-click中写的表达式，能使用js原生对象上的方法，比如math.max之类的吗

不可以。只要在页面中，就不能直接调用原生的js方法，因为这些并不属于在页面对应的controller的$scope,除非在$scope中添加这个函数


##  下面这种表达实例，实现后面的参数通过什么方法自定义

```html
{{now | 'yyyy-mm-dd'}}
````

filter 有两种使用方法
```html
<p>{{now | data: 'yyyy-mm-dd'}}</p>
```

另一种在js里面用

```js
$filter('date')(now, 'yyyy-mm-dd')
```

自定义
```js
app.filter('过滤器名字', function() {
    return function (需要过滤的对象,过滤参数,过滤参数) {
        return 处理后的对象
    }
})
```

## factory、service和provider是什么关系？

factory
把service的方法和数据放在一个对象里面，并返回一个对象

```js
app.factory('FooService', function(){
    return {
        target: 'factory',
        sayHello: function(){
            return 'hello ' + this.target;
        }
    }
});
```

service
通过构造函数方式创建service，返回一个实例化对象

```js
app.service('FooService', function(){
    var self = this;
    this.target = 'service';
    this.sayHello = function(){
        return 'hello ' + self.target;
    }
});
```


provider
创建一个可通过config配置的service,$get中返回的，就是用factory创建的service


```js
app.provider('FooService', function(){
    this.configData = 'init data';
    this.setConfigData = function(data){
        if(data){
            this.configData = data;
        }
    }
    this.$get = function(){
        var self = this;
        return {
            target: 'provider',
            sayHello: function(){
                return self.configData + ' hello ' + this.target;
            }
        }
    }
});

// 此处注入的是 FooService 的 provider
app.config(function(FooServiceProvider){
    FooServiceProvider.setConfigData('config data');
});
```

从底层实现来看，service调用了factory,返回其实例，factory调用了provider,返回其$get中定义的内容，

$factory和service功能类似，只不过factory是普通的函数，可以返回任何东西
service是构造器，可以不返回
provider是加强版的factory,返回一个可配置的factory


## $rootscope和$scope的区别

$rootScope页面所有的$scope的父亲

angular 解析ng-app然后在内存中创建`$rootScope`
angular会继续解析找到双括号表达式表达式，并解析变量

接着会解析带有ng-controller的div然后指向某个controller函数，这个时候在这个controller函数变成一个$scope对象的实例


## 表达式是如何工作的

每出现一个双括号表达式就会设置一个$watch而$interpolation会返回一个带有上下文参数的函数，最后改函数执行，算是表达式$parse作用域上

## angular的digest周期

angular总会对比scope module的值，一般digest周期都是自动触发的，也可以使用$apply进行手动触发

## 取消 `$timeout`,停止`$watch`

取消
```js
$timeout.cancel(customTime)
```

停止 

```js

var deregisterWatchFn = $rootScope.$watch('someGloballyAvailableProperty', function (newVal) {  
  if (newVal) {
    // we invoke that deregistration function, to disable the watch
    deregisterWatchFn();
    
  }
});

```

要取消watch的话，一开始将$watch的返回值保存就好啦，要取消watch的时候，在调用。


## angular directive中restrict中分别怎么设置

A 属性匹配
E 标签匹配
C class匹配
M 注释匹配

在scope中

@ 设置一个字符串
= 双向绑定
& 用于执行父级scope上的一些表达式

## $apply 和 $digest的区别

$apply 可以接收一个参数作为function，这个参数会被包装在try..catch中，一旦异常就会被$exceptionHandler service 处理

$apply会使ng进入$digest cycle,并从`$rootScope`开始遍历检查数据变更

$digest仅会检查scope和它的子scope

## 写controller逻辑 需要注意

简化代码

尽量减少dom节点操作，dom最好出现在指令中，angular提倡驱动开发，service或者controller中出现dom操作意味这，测试驱动无法通过，angular专注上相数据绑定，无需关系一对dom操作

## angular是mvc还是mvvm
首先说明一下mvc 和 mvvm理解

为什么需要mvc,随着代码规模化，必须切分职责，方便后期的维护，修改一块功能，不能影响其他功能，也方便复用，mvc只是一种手段，终极目标的模块化和复用

mvvm的优点

低耦合 可复用  独立开发 可测试

angularjs的mvvm 模式分为四个部分

- view 专注界面的显示和渲染，在angular中则是包含一堆声明式的directive视图模板

- viewModel 是view和model的粘合体，负责view和model的交互和协作，负责给view提供显示的数据，以及view中事件的操作

- model  它是应用程序的业务逻辑相关的数据的封装载体，是业务领域的对象，model不会关心如何显示或操作，大部分model来自服务端返回数据或者全局配置 
  angualr的service则是封装和处理相关业务逻辑的场所
  
- controller 它负责初始化viewModel，将组合一个或者多个service来获取业务领域model放在viewModel对象上，使得应用界面在启动加载的时候达到一种可用的状态
 
 
 ## angularjs 核心 
 
 mvc  模块化  双向绑定  语义化标签 注入依赖


## directive中compile和link的区别

使用compile函数可以改变原始的dom,在ng创建原始dom实例以及创建scope之前

可以应用于当需要生成多个element实例，compile函数阶段改变原始的dom生成多个原始dom节点,然后每个又生成element实例.因为compile只会运行一次,所以当你需要生成多个element实例的时候是可以提高性能的.

- pre-link 可以运行一些业务代码在ng执行完compile函数之后，但是在它所有子指令的post-link函数将要执行之前

- post-link 来执行业务逻辑，这个阶段，它已经知道所有的子指令已经编译完成并且pre-link以及post-link函数已经执行完成

当同时设置compile和link函数，compile所返回的函数当做link函数，而link选择本书则会被忽略，如果注释掉compile，link就起作用了 


## 默认的angular路由中，#号怎么去掉

这个原因是因为angular框架提供一种html5模式的路由
可以设置$locationProvider
```js
$locationProvider.html5Mode(true)
```
缺点是无法刷新，404
需要配置服务器来修复


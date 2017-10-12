---
title: ajax原理
date: 2017-04-05 13:03:19
categories: miaov
tags: [ajax]
toc: true
---

ajax 学习总结

<!-- more -->

# 什么是ajax?

异步的的js和xml,用js异步形式操作xml,工作主要是数据交互
- 借阅用户操作时间，减少数据请求，可以无刷新请求数据

# 创建一个对象

```js
 oBtn.onclick = function () {
     // 打开浏览器

     var xhr = new XMLHttpRequest();
     // 打开地址
     xhr.open('get', './1.txt', true);
     // 提交
     xhr.send();
     // 等待服务器返回内容
     xhr.onreadystatechange = function () {
         if(xhr.readyState === 4) {
             alert(xhr.responseText);
         }
     }
 }
```

不兼容ie6一下，需要写判断 window.XMLHttpRequest

```js
try {
    
} catch(e) {
    
}

// 代码尝试执行这个`try`块中的内容，如果有错误，则会执行catch{}不会影响程序执行
```

```js
xhr = new XMLHttpRequest();
```

# open 方法

参数 打开的方式，数据地址，是否异步

异步： 非阻塞 前面的代码不会影响后面代码的执行 setTimeout true  一般用异步的形式
同步： 阻塞 当前面的工作没有做完，后面会阻塞 比如jq库的加载 false 后续的内容需要前面挂钩，

# 表单 数据提交

action: 数据提交的地址，默认的当前页面
method: 数据提交的方式，默认get方式

- get 
  把数据名称和数据值用=连接，如果有多个数据，会用&进行连接，然后把数据放到url?后面
  不要通过get传递过多的数据，数据多少根据各个浏览器决定

- post 
  数据在请求头，发送数据是串联形式，但是位置不一样，理论上没有限制
  enctype: 提交的数据格式，默认application/x-www-form-urlencoded
  需要在表格中 `method="post"`


> get请求和post一般用于何种需求
  
  get 方式的历史记录会被记录，影响用户隐私，不太安全，只能传字符串
  
  post 通过请求头，可以用于多种数据类型


# 数据的获取

怎么得到ajax 返回的数据

`xhr.responseText`: 返回的数据会放在这个属性，类型是字符串类型

`readyState`: ajax工作状态 0 1 2 3 4

- 0 初始化
- 1 调用`xhr.send()`
- 2 载入完成，请求已经发送，收到响应内容
- 3 解析 正在解析内容
- 4 解析完成

`onreadychange` 当状态值改变的时候触发

```js
xhr.onreadystatechange = function() {
  if(xhr.readyState = 4) {
      // code
  }
}
```

请求资源的时候，服务器会返回一个状态值

- 1 代表消息
- 2 成功类型
- 3 重定向 调到其他页面，缓存就是一种重定向
- 4 错误
- 5 服务器性错误

可以做更好的容错处理
```js
xhr.onreadystatechange = function() {
  if(xhr.readyState = 4) {
      if(xhr.status === 200) {
          // 请求正确
      } else {
          alert('error'); 
      }
  }
}
```

# 请求的处理

- get 请求文件，传输数据

```js
var oBtn = document.getElementById('btn');

oBtn.onclick = function () {
	var xhr = new XMLHttpRequest();
	xhr.open('get','form.get.php?username=may&age=30',true);
	xhr.send();

	xhr.onreadystatechange = function () {
		if(xhr.readyState === 4) {
			if (xhr.status === 200) {
				alert(xhr.responseText)
			} else {
				alert('error')
			}
		}
	}
}

```

注意：

1 如果不想存在缓存问题 可以给请求后面加随机数或者时间戳

```js
xhr.open('get','form.get.php?username=may&age=30&' + new Date(),true);
```
2 中文乱码问题，可以用编码 encodeURI

```js
xhr.open('get','form.get.php?username='+encodeURI('文件') +'&age=30&' + new Date(),true);
```

- post 方式请求文件

数据作为参数放在send方法里面，并且设置请求头

```js
var oBtn = document.getElementById('btn');
oBtn.onclick = function (){
	var xhr = new XMLHttpRequest();
	xhr.open('post', 'form.post.php', true);

	xhr.setRequestHeader('content-type', 'application/x-www-form-urlencoded'); // 需要告诉后端发送数据的类型
	xhr.send('username=may&age=30');

	xhr.onreadystatechange = function () {
		if(xhr.readyState === 4) {
			if(xhr.status = 200) {
				alert(xhr.responseText);
			}
		}
	}	
}
```

post没有缓存问题，它本身就是提交数据
post没有转码的问题


# 数据获取的问题

- JSON.stringify(arr): 可以把一个对象转成对应字符串
- JSON.parse(str); key值必须是双引号才可以转为json

```js
var oBtn = document.getElementById('btn');
var oul = document.getElementById('ul');

oBtn.onclick = function () {
	var xhr = new XMLHttpRequest();
	xhr.open('get', 'getNew.php', true);
	xhr.send();
	xhr.onreadystatechange = function () {
		if(xhr.readyState === 4) {
			if(xhr.status === 200) {
				var data = JSON.parse(xhr.responseText);
				var html = '';
				for (var i = 0; i < data.length; i++) {
					html += '<li><a href="">' + data[i].title + '</a><span>' + data[i].date+ '</span></li>'
				}

				oul.innerHTML = html;
			}
		}
	}
}
```

# 瀑布流展示demo

## 瀑布流布局需求

 - 每次最短的li,然后添加位置
 - 需要知道图片的高度，否则图片加载还没有加载完成，就会自动计算那一列最短而去添加。可以有两种解决方案
 - 拉到最底下加载

计算最短的li的高度 + top值 < 可视区的高度 + 滚动条的高度 `document.documentElement.scrollTop || document.body.scrollTop`

- 往下拉， 不仅仅加载一次，可能会触发 第二页 第三页，可以设置个标识位

> 数据加载完的时候

 应该对每次取到的数据进行判断，如果没有数据， return 出去

> 后端传值宽高 如果宽度确定  200 高度就是

```js
element.height * (200/element.width)

document.createElement('li');
```

- 预加载

# 跨域解决

一个域名的文件去请求和它不一样的域名下的资源文件，就会产生跨域请求

浏览器因为安全问题不允许跨域请求，可以采用

- 通过本地代理 本地建立一个代理，这个代理请求服务器，需要资源的时候，在请求服务器
- 通过flash flash请求资源，服务器端有xml文件，里面存一些资源域名，如果能找到允许你访问
- 通过jsonp （json with Padding） 

从另外的域名，把资源拿取过来
1. script标签， 可以加载.js以外的文件类型吗？文件的后缀名是辨识的不是文件类型决定， 跟根据文件里面的实质内容。只认内容，不认文件名字
2. 用script标签加载资源没有跨域问题


在资源加载进来之前定义好一个函数，这个函数接收一个参数（数据），函数利用这个参数做一些事情
然后需要的时候通过script标签加载对应远程文件资源，当远程的问价资源被加载进来的时候，就会去执行我们前面定义好的函数，并且把数据当做这个函数的参数传入进来

>  如果想实现按需加载，比如当用户点击的时候？

当用户点击的时候创建 `script` 标签，加载到 `body`标签后面，从而实现按需加载




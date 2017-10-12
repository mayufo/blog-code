---
title: dom详解
date: 2017-03-13 22:48:08
categories: miaov
tags: [dom, js]
toc: true
---

用mvc框架多了，已经忘了dom

看dom详解，做的一些笔记。方便以后巩固加强，方便自己查询。

<!-- more -->

# 初始DOM

## js由三部分组成
`ECMAscript` 是js的核心标准，也是一个解析器
`DOM` 是document提供的一些方法或熟悉用来操作页面
`BOM`是window提供的一些方法或者属性用来操作浏览器 （拉倒底部数据加载）

## DOM (document object model) 文本对象模型 就是document提供一些api赋予开发者操作页面的能力 
 DOM结构是树形，顶端为DOM -> 根元素 <html>-> ...
一般分为三大关系
 - 父级关系 只有一层上下级关系（从当前往上走）
 - 子级关系 只有一层以下一层的关系 
 - 兄弟关系 同一个父级 （同一级）

## 按照层级划分
 - 父子节点： 上下两层节点之间的关系
 - 祖先节点： 当前节点上面的所有节点
 - 子孙节点： 当前节点下面的所有节点的统称
 
# 节点类型

## 按照节点的类型划分
  查看某个节点类型 `nodeType` 返回一个数字 表示节点的类型
  整个页面都是节点都可以看做节点
    节点分类
  - 元素节点   nodeType: 1  element 就是一个标签
  - document nodeType: 9 
  - 元素中的文字(包括空格回车换行) nodeType: 3
  - 注释节点 nodeType: 8
  - `attributes` 代表元素的属性  是一个集合 `nodeType`代表数字2 
  
  找到属性查看值`nodeValue`, 
  
  `nodeName`查看节点的属性名 如果是`<p>` 输出P  如果是`<div>` 输出DIV  如果是文字输出 #text
  
 # 子节点 
  
  ## `childNodes` 某个节点下的所有子节点，是一个**类数组**下标为0 的时候可以打出标签的内容
   
```html
<div id='div'>12345</div>
```

```js
var odiv = document.getElementById('div');
console.log(odiv.childNodes) // [text] 类数组
console.log(odiv.childNodes[0]) // 12345
console.log(odiv.childNodes[0].nodeType)
```
> demo https://jsfiddle.net/mayufo/6p26L8wd/


```html
<div id='div'><!--注释-->2222
</div>
```
两个子节点之所以没有空格 因为空格回车代表节点3类型

```js
var odiv = document.getElementById('div');

console.log(odiv.childNodes[0].nodeType); // 8
console.log(odiv.childNodes[1].nodeType); // 3
console.log(odiv.childNodes.length); // 2
```

> demo https://jsfiddle.net/mayufo/qk3znLft/

 > 注释节点如果查看注释的值?
   div.childNode[0].nodeValue
    

> demo 当点击页面的时候  li 改变宽度


```css
li {
    transition: 1s;
    width: 20px;
    height: 20px;
    background: red;
}
```

```html

<ul id="ul">
       <li>1</li>
       <li>2</li>
       <li>3</li>
       <li>4</li>
       <li>5</li>
</ul>
```
## `childNodes`查找到除元素外还能查找到空格回车元素

```js
var ul = document.getElementById('ul');
var lis = ul.childNodes;  // 11 个 包括空格和换行  <li>标签前后的
document.onclick = function () {
    for (var i = 0; i < lis.length; i++ ){
        if(lis[i].nodeType === 1 ) {
        lis[i].style.width = '150px';
       }
   }
}
```


 ## `children`: 不是标准的属性，但是所有浏览器都支持，找到摸个元素下所有元素子节点

```js
var ul = document.getElementById('ul');
var lis = ul.children;  // 5 
 document.onclick = function () {
    for (var i = 0; i < lis.length; i++ ){
        lis[i].style.width = '150px';
   }
}
```

> demo https://jsfiddle.net/mayufo/0h1yfdox/


# 父节点

`parentNode`查找某个元素的父节点

```html

<ul id="ul">
       <li>1</li>
       <li>2</li>
       <li>3</li>
       <li>4</li>
       <li>5</li>
</ul>
```

```js
 var ul = document.getElementById('ul');
 var alis = ul.children; 

console.log(alis[0].innerHTML); // 1
console.log(alis[0].parentNode); // <ul id="ul">..</ul>
console.log(alis[0].parentNode. parentNode);  // <body>

console.log(alis[0].parentNode. parentNode. parentNode); // 页面中最大的父元素是document 再大就是null
```
> demo https://jsfiddle.net/mayufo/27q1dacp/

> parentNode 例子

```html 
<ul>
    <li><a href="javascript:;">11</a></li>
    <li><a href="javascript:;">22</a></li>
    <li><a href="javascript:;">22</a></li>
</ul>
```

```js
var a = document.getElementsByTagName('a');
for (var i = 0; i < a.length; i++) {
    a[i].onclick = function () {
        this.parentNode.style.display = 'none';
   }
}
```

> demo https://jsfiddle.net/mayufo/5r41hv2e/

# 兄弟节点

`nextElementSibling` 找到某个元素的下个兄弟节点 

> nextElementSibling举例

```html 
<ul id='ul'>
    <li><a href="javascript:;">11</a></li>
    <li><a href="javascript:;">22</a></li>
    <li><a href="javascript:;">33</a></li>
</ul>
```

```js
var ul = document.getElementById('ul');
var alis = ul.children;

console.log(alis[0].nextElementSibling);  // <li>22</li>
console.log(alis[0].nextElementSibling. nextElementSibling); // <li>33</li> 超出为null
```

`previousElementSibling` 找到某个元素的上个兄弟节点 

> previousElementSibling 举例同 nextElementSibling

`firstElementChild`找到第一个子节点

`lastElementChild` 找到最后一个子节点

```js
var ul = document.getElementById('ul');

ul.firstElementChild; // <li>111</li>
ul.lastElementChild; // <li>333</li>
```

# offsetLeft、offsetParent

`offsetParent` 最近的有定位属性的祖先节点, 如果父节点都没有定位，默认定位是`body`，否则是定位该元素父级上设置`position: relative的元素`。

```html
<div id="div1">
    <div id="div2">
        <div id="div3"></div>
    </div>
</div>
```

```js
var div3 = document.getElementById('div3');
    console.log(div3.offsetParent);
```

```css
div {
    padding: 100px;
}
#div1 {
    background: red;
    position: relative;
}

#div2 {
    background: blue;
}

#div3 {
    background: green;
}
```

> demo https://jsfiddle.net/mayufo/pnkcogjy/

# offsetLeft、offsetTop

`offsetLeft` 外边框到有定位父级的内边框的距离, 如果没有父级` position: relative`的定位，子级也没有`position: absolute`,定位默认到body。和offsetParent有关, 得到的数字没有单位。

`offsetTop` 上边框到有定位父级的上呗边框的距离

这里注意没有`offsetRight`和`offsetBottom`

使用场景：方便获取元素的位置

`getComputedStyle(div3).left` 也可以得到，有单位。


> 例子 当点击按钮，将div3移动到左顶边

```html

<input type="button" id="btn" value="btn">

<div id="div1">
    <div id="div2">
        <div id="div3"></div>
</div>
</div>
```
```css

*{
	margin: 0;
	padding: 0;
}
#div1{
	background: red;
	width:100px;
	height:100px;
	margin-left: 50px;
	position: relative;
	border: 10px solid #000;
}
#div2{
	background: blue;
	position: relative;
	width:60px;
	height:60px;
	top:20px;
	left:30px;
	border: 10px solid #000;
}
#div3{
	width:30px;
	height:30px;
	background: yellow;
	position: absolute;
	top:20px;
	border: 1px solid #000;
	left:0;
	transition:1s left;
}
```
```js
var div3 = document.getElementById('div3');
var btn = document.getElementById('btn');
var div3Border = parseInt(getComputedStyle(div3).borderLeftWidth); // 减去自身的边框


btn.onclick = function () {
    var left = 0;
    var elem = div3;
    while(elem) {
        left += elem.offsetLeft + parseInt(getComputedStyle(elem).borderLeftWidth);
        elem = elem.offsetParent;
    }
    
    left -= div3Border;
    
    div3.style.left = - left + 'px';
}
```

> demo https://jsfiddle.net/mayufo/q7d1bozj/

# 计算绝对位置getBoundingClientRect()

**getBoundingClientRect()**获取某个元素的信息，left、top、bottom、right、width、height,返回值是一个对象。

注意： 获取的值会根据滚动条变化

```html
<input type="button" id="btn" value="btn">

<div id="div1"></div>
```

```js
var div = document.getElementById('div1');
var btn = document.getElementById('btn');

btn.onclick = function () {
	console.log(div.getBoundingClientRect()) // bottom:130,height:100,left:58, right:158, top:30, width:10
}
```

> demo https://jsfiddle.net/mayufo/cL50o17d/

# 元素属性操作

**getAttribute**获取元素属性,操作某个元素的行间属性.不是行间无法获取到。

```html
<div id="div1" index="2"></div>
```
```js

var div = document.getElementById('div1');
// 想获取id的值
console.log(div['id']); // div1

console.log(div.getAttribute('id'));

// 如果是div.index = 2 直接设置，getAttribute无法获取到index的值

div.index = 2;

console.log(div['index']); // 主要取对象属性的值，有时候不能取到行间属性的值
console.log(div.getAttribute('index')); // 主要取对象属性的值，无法取到对象属性设置的值

div.setAttribute('index', '4'); 

// 删除元素行间属性

div.removeAttribute('index');

```

> demo https://jsfiddle.net/mayufo/ng629u96/

**setAttribute**设置元素的行间属性

**removeAttribute** 删除元素的行间属性

*上面三种方法可以获取href或src的相对路径*

如果图片中存在中文的路径，打出图片地址的时候，有的浏览器中文的路径会显示乱码。所有通常情况不能直接拿此`console.log(img.src)`作为判断

可以通过`getAttribute`来得到写的路径


# 元素宽高的获取

`clientWidth` 可视内容的宽度，没有单位，不计算边框border, 不会计算padding， 不计算margin

`clientHeight` 可视内容的高度，没有单位，不计算边框border, 不会计算padding, 不计算margin

注意：如果div内容超出，只算div显示的宽高

```css
#div1 {
    height: 200px;
    width: 200px;
}
```
```html
<div id="div1"></div>
```

```js
var odiv = document.getElementById('div1');
console.log(odiv.clientWidth);
console.log(odiv.offsetWidth);
```

`offsetWidth` 获取某个元素的宽度，会计算border的, 计算padding, 不计算margin

`offsetHeight` 获取某个元素的宽度，会计算border的, 计算padding, 不计算margin

`document.documentElement.clentWidth` 可视区的宽度

`document.documentElement.clentHeight` 可视区的高度

> 让一个不确定宽高的元素居中显示

元素的left = （可视区 - 元素的宽）/ 2

元素的top = (可视区 - 元素的高) / 2

```html
<div id='div1'>2222</div>
```

```css
#div1 {
  height: 100px;
  width: 100px;
  background: red;
  position: absolute;
}
```

```js

var odiv = document.getElementById('div1'); // 记得div1 position: absolute

var clientW = document.documentElement.clientWidth;
var clientH = document.documentElement.clientHeight;

console.log(odiv);
var iW = odiv.offsetWidth;
var iH = odiv.offsetHeight;

odiv.style.left = (clientW - iW) / 2 + 'px';
odiv.style.top = (clientH - iH) / 2 + 'px';
```

> demo https://jsfiddle.net/mayufo/eLLuspjp/

# 创建元素

> 点击添加元素
  
  - 可以通过字符串的拼接
  
  - 通过createElement('标签的名字'), 如果创建没有的标签，也会出现自定义的标签
  
  - 插入元素 `parentNode.appendChild(childNode)` 向父级的**尾部**添加一个元素 
  
# 插入元素  
  - 插入元素 `parentNode.inserBefore` (新添加的元素， 添加的元素位置，会添加到这个元素之前) 向父级中的**某个元素前插入元素** 特性 如果第二个参数为假的，则将这个元素添加到父元素的第一个
  
  - 删除元素 `父级.removeChild（自定的子级点)` 如果指定的子节点没有会报错

```html
<input type="button" id="btn" value="button">
<ul id="ul">
    <li>0</li>
</ul>
```


```js
    var btn = document.getElementById('btn');
    var oul = document.getElementById('ul');
    
    var html = '';
    var num = 0;
    
    btn.onclick = function() {
      num++;
      html += '<li>' + num + '</li>';
      
      oul.innerHTML = html;
    };
    // appendChild
    btn.onclick = function() {
      num++;
      var li = document.createElement('li'); //创建li标签
      li.innerHTML = num;
      ul.appendChild(li);
      
      console.log(li.nodeType); // 1 元素节点
    };
    
    // insertBefore
     btn.onclick = function() {
          num++;
          var li = document.createElement('li'); //创建li标签
          li.innerHTML = num;

          ul.insertBefore(li, ul.children[0]);// 将ul的第一个子节点插入元素
          
          console.log(li.nodeType); // 1 元素节点
        };
     // removeChild
      btn.onclick = function() {
             if(ul.lastElementChild) {
                 ul.removeChild(ul.lastElementChild);
             }
         }
```
> demo https://jsfiddle.net/mayufo/vyzxgmnf/1/


 
 # 元素替换
 
 > 当点击按钮的时候，将红色替换成蓝色
 
```html
<input type="button" id="btn" value="button">
<div id="box">
    <div id="red"></div>
    <hr id="hr">
    <div id="blue"></div>
</div>

```

```js
var red = document.getElementById('red');
var hr = document.getElementById('hr');
var blue = document.getElementById('blue');
var btn = document.getElementById('btn');
var box = document.getElementById('box');


btn.onclick = function () {
    box.replaceChild(red, blue);
}

```
 > demo https://jsfiddle.net/mayufo/ofp5Lv19/

 -  `父级.replaceChild(node(要替换成什么), childNode（谁被替换）)` 都是剪切的操作
 
 # 克隆
  
 > 将蓝色方块克隆，放到红色方块下
 
 - `要复制的元素.cloneNode(false);`  克隆某个元素，克隆只克隆元素本身，克隆不会克隆下面的子节点, 如果参数改为true 就可以默认赋值子节点,默认值为false. 
  事件不会被克隆的 
  克隆才是真正的赋值
  
  ```js
  var red = document.getElementById('red');
  var hr = document.getElementById('hr');
  var blue = document.getElementById('blue');
  var btn = document.getElementById('btn');
  var box = document.getElementById('box');
  
  var elem = blue.cloneNode();
  box.insertBefore(elem,hr);
  ```
  > demo https://jsfiddle.net/mayufo/cymoh67c/
 
 # 表格
 
 ```html
 <table>
    <thead>
        <tr>
            <th>head1</th>
            <th>head2</th>
            <th>head3</th>
            <th>head4</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>content1</td>
            <td>content2</td>
            <td>content3</td>
            <td>content4</td>
        </tr>
    </tbody>
    <tbody>
        <tr>
            <td>content1</td>
            <td>content2</td>
            <td>content3</td>
            <td>content4</td>
        </tr>
    </tbody>
    <tfoot>
        <tr>
            <td>foot1</td>
            <td>foot2</td>
            <td>foot3</td>
            <td>foot4</td>
        </tr>
    </tfoot>        
</table>
 ```
 
 ```js
 var table = document.getElementById('box');
 
 table.tHead.style.borderColor = 'red';
 
 
 table.rows[1].style.background= 'blue';
 
 console.log(table.tBodies);
 
 table.tBodies[0].style.background = 'red';
 
 table.rows[1].cells[2].style.background = 'pink';

 ```
 
 `table.tHead` 选取表格的表头
 `table.rows` 获取tr  获取表格中所有的`tr`, 得到一个数组，指定某个需要下标 表头和表尾也会算入tr的list中, 比`tbody`优先级高
 `table.tBodies` 获取表格中所有的`tbody`, 得到一个数组，指定某个需要下标
  
  `table.row[n].cells[i]` 获取表格中其中一个`td`,需要指定下标。前面也可以结合`tBodies`
  
  > demo https://jsfiddle.net/mayufo/aum5cz9n/
  
  渲染一个表格
  
  > demo https://jsfiddle.net/mayufo/uLLmm7t6/
  
  #练习
     - 留言板 https://jsfiddle.net/mayufo/4u5v6tno/2/
     
     - 上移下移 https://jsfiddle.net/mayufo/sLj50rwx/
  
     - 排序 https://jsfiddle.net/mayufo/LvenLyy3/
 

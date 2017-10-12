---
title: jquery回顾性学习
date: 2017-04-19 13:16:34
categories: miaov
tags: [jquery, 回顾，总结]
toc: true
---

回顾性学习！

<!-- more -->

# 选择元素

```html
<ul>
    <li></li>
    <li></li>
    <li></li>
    <li></li>
</ul>
```

```js
$('li:first').css('background', 'red'); // 选择第一个
$('li:last').css('background', 'red');  // 选择最后一个
$('li:eq(2)').css('background', 'red');  // 选择底三个
$('li:event').css('background', 'red');  // 奇数行
$('li:odd').css('background', 'red');  // 偶数行
```

筛选查 `class = box`

```html
<ul>
    <li></li>
    <li class="box"></li>
    <li class="box"></li>
    <li></li>
</ul>
```

```js
$('li .box').css('background', 'red')

$('li').filter('.box').css('background', 'red')

```
筛选出 `title`

```html
<ul>
    <li title="hello"></li>
    <li></li>
    <li></li>
    <li></li>
</ul>
```

```js
$('li').filter('[title=hello]').css('background', 'red');
```

# 写法

- 方法函数化

- 链式操作

- 取值赋值合体


# jq与js的关系

- 可以共用，但不能混用


# 常用方法

`has()` 是否包含

`attr()` 对属性进行操作

`filter()` 对一组元素进行筛选， 满足条件留下来

`not()` 与filter正好相反


filter 和 not 针对的是当前自身的元素
而针对的是包含关系的元素

`next()` 下一个元素的兄弟节点，同级关系
`prev()` 下一个元素的兄弟节点

`find()` 

```js
$('div').find('h2').css('background', 'red');
```

`eq` 下标的作用

```js
$('div').find('h2').eq(1).css('background', 'red');
```

`index` 索引 在所有兄弟节点之间的位置

```html
<h3></h3>
<h3></h3>
<h3 id="h"></h3>
<h3></h3>
<h3></h3>
<h3></h3>
```

```js
alert($('#h').index()); // 2
```

# 编写选项卡


原生 

```html
window.onload = function () {
        var odiv = document.getElementById('div1');
        var aInput = odiv.getElementsByTagName('button');
        var aCon = odiv.getElementsByTagName('div');

        for (var i = 0; i < aInput.length; i++) {
            aInput[i].index = i;
            aInput[i].onclick = function () {
                for(var i = 0; i < aCon.length; i++) {
                    aInput[i].className = '';
                    aCon[i].style.display = 'none';
                }
                this.className = 'active';
                aCon[this.index].style.display = 'block';
            }
        }
    }
```


```html
$('#div1').find('button').click(function () {
        $('#div1').find('button').attr('class', '');
        $('#div1').find('div').css('display', 'none');
        $(this).attr('class', 'active');
        $('#div1').find('div').eq($(this).index()).css('display', 'block');
    })
```

# 常用方法

- addClass() // 如果之前有，再添加重复的，它不会再添加

- removeClass() // 删除class

- width() // 实际的宽

- innerWidth() // 含实际宽+padding

- outerWidth() // 含实际宽+padding+border 没有margin

- outerWidth(true) // 含实际宽+padding+border+margin

# 插入位置的操作

- insertBefore() // 将前面找到的元素放在后面元素的前面  对应before()

前

```html
<div>div</div>
<span>span</span>
```

```js
$('span').insertBefore($('div'));
// 相当于
$('div').before($('span'));
```

后

```html
<span>span</span>
<div>div</div>
```

- insertAfter() // 和inserBefore相反，前面的元素放在后面元素的后面 对应after()

前

```html
<div>div</div>
<span>span</span>
```

```js
$('div').insertAfter($('span'));
// 相当于
$('span').after($('div'));
```

后


```html
<span>span</span>
<div>div</div>
```

- appendTo() 等于 appendChild 对应append()

前
```html
<div>div</div>
<span>span</span>
```

```js
$('div').appendTo($('span'));
// 相当于
$('span').append($('div'));
```

后

```html
<span>
    span
    <div>div</div>
</span>
```

- prependTo() 对应prepend


前
```html
<div>div</div>
<span>span</span>
```

```js
$('div').prependTo($('span'));

// 相当于

$('span').prependTo($('div'));
```
后

```html
<span>
    <div>div</div>
    span
</span>
```
> 区别

后续的操作变了

```js
$('span').insertBefore($('div')).css('background', 'red'); // span变红
// 相当于
$('div').before($('span')).css('background', 'red'); // div变红
```

- remove() 删除节点

- on() 事件的另外一种写法 可以针对自定义事件来触发 也可以针对多个事件

```js
$('div').click(function() {
    console.log(1)
});

$('div').on('click mouseover', function() {
      console.log(1)
}); // 可以针对自定义事件来触发 也可以针对多事件
```

// 鼠标点击是234 鼠标移入是 456
```js
$('div').on({
    'click': function() {
      alert(234);
    },
    'mouseover': function() {
      alert(456);
    }
})
```

- off() 触发一次就取消

```js
$('div').on('click', function() {
  alert(123);
  $('div').off();
})
```
```js
$('div').on('click mouseover', function() {
      console.log(1);
      $('div').off('mouseover'); // 可以针对其中的一个事件进行取消
}); 
```

- strollTop() // 获取X轴滚动距离


# 编写弹窗



```js
$('#login').click(function () {
        var odiv = $('<div class="modal1">1111111</div>');
        $('body').append(odiv);
        odiv.css('left', ($(window).width() - odiv.outerWidth())/2);
        odiv.css('top', ($(window).height() - odiv.outerHeight())/2 + $(window).scrollTop()) ;
    });

$(window).on('resize scroll', function () {
    odiv.css('left', ($(window).width() - odiv.outerWidth())/2);
    odiv.css('top', ($(window).height() - odiv.outerHeight())/2 + $(window).scrollTop()); // 加上滚动条的距离
})
```

```js
    $('.clocse').click(function() {
      $('#login').remove();
    })

```

# 事件细节

- ev : 原生是event 
- ev.pageX(相对于文档) 鼠标的坐标 :   原生用的clientX(相对于可视区)    可视区 + 滚动 = 相对于文档
- ev.which : 原生keyCode 键盘的键值  回车是13，which 还可以记录鼠标的左右和滚动
- ev.preventDefault(); 阻止默认事件
- ev.stopPropagation() 组织冒泡事件
- return false; 即阻止默认事件又阻止冒泡事件
- one 事件只运行一次
```js
$('div').one('click', function(ev) {
  console.log('只执行一次')
})
```

# 位置方法

- (原生)offsetLeft 如果外面有个绝对定位 ，left向左就是到相对定位的距离，如果没有相对定位就是到屏幕左边

- offset() 有两个属性 left() 和 top（）无视定位 可以获取到屏幕的总距离

- position() 有两个属性 top() 和 left() 会把当前元素转化为类似定位的形式，忽视margin的值，

```html
<div class="div1">
    <div class="div2"></div>
</div>
```

```css
#div1 {width: 200px; height: 200px; background: red; overflow: hidden; margin: 20px;}

#div2 { width: 100px; height: 100px; background: green; margin: 30px; padding: 20px;}
```

```js
alert($('#div2').position().left); // 20px  到有定位的父级， 忽视margin 则是20px 如果div1 加position: absolute则会变为0， 如果给div1 加position: absolute，div2 加position: absolute; left: 25px 弹出 25px
```

# 获取父级

- parent() 获取父级

- offsetParent() 获取有定位的父级 如果外面没有定位就是body

- size() 获取一组元素长度 有点像length

- each() 循环  // 第一个参数是下标，第二个参数是每个元素

```js
$('li').each(function (index, elem) {
    $(elem).html(i)
})
```

# 编写拖拽

```js
var disX = 0;
    var disY = 0;
    $('div').mousedown(function (ev) {
        disX = ev.pageX - $(this).offset().left;
        disY = ev.pageY - $(this).offset().top;
        $(document).mouseover(function (ev) {
            $('div').css('left', ev.pageX - disX);
            $('div').css('top', ev.pageY - disY);
        })

        $(document).mouseup(function () {
            $(document).off();
        })
        return false;
    })
```
# 其他常用

- hover 模拟hover()


```js
$('#div1').hover(function() {
  $(this).css('background', 'blue'); // 鼠标移入
}, function() {
  $(this).css('background', 'red'); // 鼠标移出
})
```

- show() hide ()  接收一个参数 1000 为1s的运动效果 
- fadeIn() fadeOut() 淡入 淡出 接收一个参数为时间 in  -> out 从透明0 到 1

```js
$('#div1').hover(function() {
  $(this).fadeIn();
}, function() {
  $(this).fadeOut();
})
```

- slideUp()  卷出 / sideDown()  卷起

- fadeTo()  一个参数是时间 第二个参数是透明度

# 高级

## 基础方法扩充

- get(): 把jq转为原生js



```html
<div class="div1"></div>

<ul>
<li></li>
<li></li>
<li></li>
</ul>
```

```js
$('#div1').get(0).innerHTML;


for(var i = 0; i < $('li').get().length; i++) { // $('li').get() 会将一组li转为原生的 
    $('li')[i].style.background = 'red';
}
```

而此处的$(li).length 也是可以用，因为jq的length也是个方法， 如果给$('li')加下标 也是可以转为原生的


- outerWidth() 如果参数写true 会获取margin的值

- (原生)offsetWidth 获取不到隐藏元素,而jq中获取元素的宽高，可以获取到隐藏的元素

- text()  会获取所有的内容

```html
<div>111 <span>444</span></div>
<div>222</div>
<div>333</div>
```

```js
$('div').html() //如果用html() 获取到 111<span>4444</span>  

$('div').text() // 获取到 111444222333

$('div').text('<h3>h3</h3>'); // 得到文本<h3>h3</h3>
```

- remove() 删除后的返回值就是之前删的元素，但是它自带的事件和操作都会被删掉

```js
$('div').click(function () {
    alert(123);
})

var oDiv = $('div').remove();

$('body').append(oDiv); // 但是无法恢复事件
```
- detach() 跟remove方式一样，会保留删除的事件


-  window.onload = function() {} 所有页面加载完

- $(function () {}) // 等dom 加载完，比原生性能好

- $(document).ready(function () {}) 就是上面的$(function () {}) // dom加载完成

- parent() // 获取父级  parents() //获取当前元素所有的祖先节点,参数是筛选功能，只要条件满足 就能筛选不来，不止一个
 
```js
$('#div2').parents('body').css('background', 'red'); // 参数是body ,只能给父级的body变为红色背景
```

```html
<div id="div1">
    <div id="div2"></div>
</div>
```
- closet() 获取最近的祖先节点，包括当前元素自身, 必须写筛选的参数，找最近只能找到一个元素

```js
$('#div2').closest('body').css('background', 'red'); // 参数是body ,只能给父级的body变为红色背景

```

- siblings() 获取所有元素的兄弟节点，不包括它自身，也可以写一个参数进行筛选

- nextAll() 找下面所有的兄弟节点 next找下一个兄弟节点， 也可以写一个参数进行筛选

- prevAll()  上面所有的兄弟节点 也可以写一个参数进行筛选

- parentsUntil() 父元素截止  nextUntil() 下一个元素截止  prevUntil()  找上一个元素截止 里面有一个参数来说明 到那个元素截止

- clone() 克隆节点 复制节点的操作， 可以接受一个参数作用可以复制之前的操作行为 参数为true ,克隆版本的也会有操作

- wrap() 包装

```js
$('span').wrap('<div>'); // 给每个span 外面包装一个div
```

- wrapAll() 整体包装

```html
<span></span>
<span></span>
<span></span>
```

```js
$('span').wrapAll('<div>'); // 会在三个span的外面包裹一个div
```
如果是

```html
<span></span>
<span></span>
<p>11</p>
<span></span>
```

使用wrapAll


```html
<div>
<span></span>
<span></span>
<span></span>
</div>

<p>11</p>
```

- wrapInner 在里面包装

```html
<span><div></div></span>
<span><div></div></span>
<span><div></div></span>
```

- unwrap 删除父级，不包括body

前

```html
<div><span></span></div>
```

```js
$('span').unwrap()
```
后
```html
<span></span>
```

- add() 对节点添加组合的一种方法


```html
<span>111111</span>
<div>2222222</div>
```

```js
var elem = $('div');
var elem = elem.add('span');

elem.css('color', red); //两个元素都会变红
```

- slice() 截取指定的范围

```html
<ul>
    <li></li>
    <li></li>
    <li></li>
    <li></li>
    <li></li>
</ul>
```

```js
$('li').slice(1,4).css('background', red);  // 选择中间三个li 结束的位置不包含
```

- serialize() 串联成字符串

```html
<form action="">
    <input type="text" name="a" value="1">
    <input type="text" name="b" value="2">
    <input type="text" name="c" value="3">
</form>
```

```js
console.log($('from').serialize());   // a=1&b=2&c=3
```
- serializeArray() 串联成数组
```js
console.log($('from').serializeArray();   // [{name='a', value='1'}, {name='b', value='2'}, {name='c', value='3'}] // 每个数组是个json

```

# jq中复杂的运动

- animate() 
    - 第一个参数 是一个json 运动的值和属性，
    - 第二个值是时间，调节快慢 默认时间是400
    - 运动形式 只有两种运动形式 （swing(满快慢) linear(匀速)）默认是swing
    - 回调函数 当运动结束运行

```js
$('div').click(function() {
  $(this).animate({width: 300, height: 300}, 400, 'swing', function () {
    alert(11);
  }); // 之前的style 宽高是100 时间是400毫秒
})
```

```js
$('div').click(function() {
  $(this).animate({width: 300}, 400, 'swing', function () {
    $(this).animate({height: 300}); // 
  }); // 走完宽以后走高
})
```

链式写法
```js
$(this).animate({width: 300}, 2000).animate({height: 300}, 2000);
```

- stop() 停止运动 阻止当前的运动，不会阻止所有运动， 如果想阻止所有后续运动  stop(true);  第二个参数如果是stop(true,true)立即停止并到指定的目标点,只针对当前的运动，而不是全部的后续运动

如果想点击停止当前的所有的运动，并且全部到都到指定的目标点

```js
$('div').finish() //立即停止到指定的目标点
```

- delay() 延迟运动

```js
$(this).animate({width: 300}, 2000).delay(1000).animate({height: 300},2000); //宽走完停顿1s再走高
```

- delegate() 事件委托

```html
<ul>
    <li></li>
    <li></li>
    <li></li>
</ul>
```

将点击事件加在ul身上
优点是省略循环操作， 提好性能
```js
$('ul').delegate('li', 'click', function() {
  this.style.background = 'red';
})
```
- undelegate() 组织事件委托的操作

```js
$('ul').delegate('li', 'click', function() {
  this.style.background = 'red';
  $('ul').undelegate(); // 取消委托
})
```

- trigger() 主动触发事件

```js
$('div').on('click', function() {
  console.log(111111);
})

$('div').trigger('click'); // 主动触发 适合自定义事件
```

# 事件细节

- ev.data  事件带过来的数据
- ev.target 事件源
- ev.type 事件类型

```js
$('div').on('click', {name: 'hello'}, function(ev) {
  alert(ev.data.name); // 弹出aaaa
})
```

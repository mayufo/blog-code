---
title: Interview_Javascript
date: 2017-04-08 00:05:25
categories: miaov
tags: [面试，js]
toc: true
---

一些比较饶人的js题的自测和学习

<!-- more -->

# lesson1 

### 1
```js
(function () {
    return typeof arguments;
}) ();  // 'object'
```
`arguments`只是个类数组

### 2

```js
var f = function g() {
  return 23;
}
typeof  g();  // error
```

### 3

```js

  (function(x){
    delete x;
    return x;
  })(1); //  1
```
delete 只能删除数组或者对象

### 4

```js
  var y = 1, x = y = typeof x;
  x; // 'undefined'
```

运算顺序从右向左

### 5

```js
 (function f(f){
    return typeof f();
  })(function(){ return 1; }); // 'number'
```

传进去整个函数 返回1

### 6

```js
  var foo = {
    bar: function() { return this.baz; },
    baz: 1
  };
  (function(){
    return typeof arguments[0]();
  })(foo.bar); // 'undefined'
```
此处this指向window arguemnts[0] 为undefined

### 7
 
 ```js
   var foo = {
     bar: function(){ return this.baz; },
     baz: 1
   }
   typeof (f = foo.bar)(); // 同上
 ```
 
 同上
 
 ### 8
 
 ```js
  var f = (function f(){ return "1"; }, function g(){ return 2; })();
   typeof f;  // 2
 ```
这种括号的运算符，会弹出最后一个值

### 9 

```js
 var x = 1;
  if (function f(){}) {
    x += typeof f;
  }
  x;  // 'iundefined'
```

`if` 里面的函数运算是true, 而f没有定义 `undfined` 

### 10

```js

  var x = [typeof x, typeof y][1];
  typeof typeof x; // 'string'
```
typeof 输出的类型是字符串类型

### 11 

```js

  (function(foo){
    return typeof foo.bar;
  })({ foo: { bar: 1 } }); // 'undefined'
```

`{ foo: { bar: 1 } }` 作为整个实参传入 而调用的只用一个属性就是 `foo`

### 12

```js
  (function f(){
    function f(){ return 1; }
    return f();
    function f(){ return 2; }
  })();  // 2
```

跟进预解析个规则，会先走一遍所有的function 

### 13 

```js
function f(){ return f; }
  new f() instanceof f; // false
```
instanceof 的作用是看前面的数据 是否是后面的的构造函数，但是如果这个构造函数里面存在return,就指向了return的数据

### 14 

```js

  with (function(x, undefined){}) length; // 2
```

形参的个数

# lesson 2 运行题

### 变量的作用域

外层的变量，内层可以找到（全局）
内层的变量，外层找不到（局部）

```js
var a = 10;
function aaa () {
    alert(a);
}

function bbb () {
    var a = 20;
    aaa();
}

bbb ();  // 10
```

涉及到作用域的问题， 运行到bbb() 返回到 bbb()函数里面 遇到aaa() ,返回aaa(),只能找到外层的10

### 变量不加 `var` 它会变成全局变量

```js
function aaa() {
  var a = b = 10;
}

aaa()

alert(a)  // 局部变量undefined
alert(b) // 全局变量 弹出 10
```

### 变量的查找是就近原则去寻找var定义的变量，当就近没有找到，就会查找外城， 如果没有找到，找外层的外层 

```js
var a = 10;

function aaa() {
  alert(a);  // 就近查找发现var, 函数做预解析， 所以变量是undefined.所以在定义变量的时候要写在上面，防止出现undefined
  var a = 20
}

aaa();  // undefined

```

```js
var a = 10;

function aaa() {
  var a = 20;

  alert(a);  // 20 寻找就近var定义的变量，
}

aaa(); 
```

```js
var a = 10;

function aaa() {
  a = 20;

  alert(a);  // 20 寻找就近var定义的变量,寻找到外层定义的a,a进函数后被替换成20
}

aaa(); 
```


```js
var a = 10;

function aaa() {

  alert(a);  // 10
    a = 20;

}

aaa(); 
```

```js
var a = 10;

function aaa() {
    
  bbb();
  alert(a);  // 10
  function bbb() {
    var a = 20;
  }
}
```


### 参数和局部变量重名的时候，优先级是等同，不分彼此

```js
var a = 10;

function aaa(a) {
  alert(a);
  var a = 20;
}

aaa(a);  // 10
```


### 基本类型赋值不存在引用关系，对象类型赋值才存在引用关系,除非对象重新赋值，导致地址断开

```js
var a = 5;
var b = a;

b += 3;

alert(a); // 8 基本类型赋值不存在引用关系
```

```js
var a = [1, 2, 3];

var b = a ;
b.push(4);

alert(a);  // [1,2,3,4]
```

```js
var a = [1, 2, 3];

var b = a ;
b = [1,2,3,4];

alert(a);  // [1,2,3]  对象赋值后，地址断开
```


```js
var a = 10; 
function aaa (a) {
    a += 3;
}

aaa(a);

alert(a);  // 10 传参尽量就相当于重新赋值，不会受函数影响

```


```js
var a = [1,2,3]
function aaa (a) {
    a.push(4)
}

aaa(a); // [1,2,3,4]对象的传参相当于引用 

alert(a);  // [1,2,3,4]
```


```js
var a = [1,2,3]
function aaa (a) {
    a = [1,2,3,4];
}

aaa(a); 

alert(a); // [1,2,3] 对象重新赋值就是断开了引用地址
```

lesson 3

> 写一个字符串转成驼峰的方式

border-bottom-color > borderBottomColor

- 课程方法
```js
function transform(str) {
        var arr= str.split('-');
        arr.forEach(function(data,index) {
            if(index>=1) {
                arr[index] = data.charAt(0).toUpperCase() + data.substring(1);
            }
        })
        return arr.join('')  
}
```

- 正则方法

```js
var str = 'border-radius';

transform(str);

function transform(str) {
    var re = /-(\w)/g   // \w就是字母的意思, 括起来的代表正则的子项
    return str.replace(re,function($0, $1) {  // 第一个参数代表正则的整体，第二个参数担保正则的子祥
        return $1.toUpperCase();
    })
}
```

> 查找字符串中最多的字符和个数

- 字符串操作

```js
var str = 'sasfdsgdsafdfagfassss';


function count(str) {
    var obj = {};
    var num = 0;
    var value = '';

    for (var i = 0; i < str.length; i++) {
        if(!obj[str[i]]) {
            obj[str[i]] = [];
        }
        obj[str[i]].push(str[i]);
    }

    for (item in obj) {
        if(num < obj[item].length) {
            num = obj[item].length;
            value = obj[item][0]
        }
    }

    return '最多出现的字母是' +value+ '重复' + num + '次';
}


count(str);
```

- 正则操作

> 如何给字符串加千分符

324423143141 > 324,423,143,141


- 自己方法

```js
var str = '1324423143141';

console.log(transform(str));

function transform (str) {
    var arr = str.split('');
    var arrh = ''
    var iNum = arr.length % 3;
    if(iNum != 0) {
        var arrh = arr.splice(0,iNum);
        arrh.push(',');      
    }
   

    for(var i = arr.length - 1; i >= 0; i--) {
    if(i%3 === 0 && i >= 3) {
        arr.splice(i, 0, ',')
    }
  }

 return arrh.concat(arr).join('');
}


```
- 课程方法
```js
var str = '1324423143141';

console.log(transform(str));

function transform (str) {
    var iNum = str.length % 3;
    var arr = [];
    var current = 0;
    var tmp ='';

    if(iNum != 0) {
        prev = str.substring(0, iNum);
        arr.push(prev)
    }

    str = str.substring(iNum);

    for (var i = 0; i < str.length; i++) {
        current++;
        tmp += str[i];

        if(current === 3 && tmp) {
            arr.push(tmp);
            current = 0;
            tmp = '';
        }
    }
    return arr.join(',')
}


```

- 正则


// (?=) :前向声明

// (?!) :反前向声明

> 匹配所有ab变成星号
```js
var str = 'abacad';

var re = /a/g;

str = str.replace(re, '*');

console.log(str)  // *b*c*d
```
> 匹配只有a后有b的才变*


```js
var str = 'abacad';

var re = /a(?=b)/g;

str = str.replace(re, '*');

console.log(str)  // *bacad 这就是前向声明的用法
```

> 匹配除了a后是b的其他，全都替换成*

```js
var str = 'abacad';

var re = /a(?!=b)/g;

str = str.replace(re, '*');

console.log(str)  // ab*c*d 和前向声明相反，匹配
```

根据上面正则，实现
```js
var str = '1324423143141';


function tranform(str) {
   var re = /(?=(?!\b)(\d{3})+$)/g;  // \b 代表开始结束的位置
   return str.replace(re, ',')
}


console.log(tranform(str));
```

> 返回一个只包含数字类型的数组 js123ldsdkfsf234234 > [123,234234]

- 字符串

```js
var str = 'js123lf234d234';
transform(str);
function transform (str) {
    var arr = str.split('')
    var tmp = '';
    var arrNew = []
    for (var i = 0; i < arr.length; i++) {
        if(!isNaN(arr[i]) && !isNaN(arr[i+1]) ){
            tmp += arr[i];
        }
        if (!isNaN(arr[i]) && isNaN(arr[i+1])) {
            arrNew.push(tmp+arr[i]);
            tmp = '';
        }
    }

    console.log(arrNew);
}
```
- 正则



# lesson 4

限制条件补全代码

> 有a，b两个变量，不用第三个变量来切换两个变量值

```js
var a = 5;
var b = 6;

 a = a + b; // 11
 b = a - b; // 6
 a = a - b;
  console.log(a);
  console.log(b); 
```
以上方法局限于数字，如果是字符串该如何呢？

```js
var a = 'hello';
var b = 'hi';

a = [a, b];
b = a[0];
a = a[1];
  console.log(a);
  console.log(b); 
```

> 有一个数n = 5, 不要循环返回 [1,2,3,4,5]

不用循环可以考虑递归，不能考虑定时器，它是异步的

- 递归

```js
var n = 5;
function show (n) {
    var arr = [];
        return (function () {
            arr.unshift(n)
            n--;
            if(n!=0) {
                arguments.callee;
            }

            return arr;
        })
}

console.log(show(n))
 
```

- 正则 利用replace 

```js
var n = 5;
function show (n) {
    var arr = [];
    var arrNew = [];
    arr.length = n+1; // 通过数组的逗号替换，所以要多一位
    var str = arr.join('a') // aaaaa
    str.replace(/a/g, function() {
        arrNew.unshift(n--)
    })
    return arrNew

}

console.log(show(n))
```


> 一个数n,当n小于100就返回n,否则返回100

```js
// if
function show(n) {
    if(n<100) {
        return n
    } else {
        return 100;
    }
}

alert(show(n))

// 三目
function show(n) {
    return n<100?n:100;
}

//switch
function show(n) {
    switch(n < 100){
        case true:
            return n;
        break;
        case false:
            return 100;
        break;        
    }
}
// Math
function show(n) {
    return Math.min(n,100)
}
// 数组
function show(n) {
    var arr = [n, 100];
    arr.sort(function (a, b) {
        a - b;
    })

    return arr[0];
}
// 循环
function show (n) {
    var m = '' + n; //转成字符串

    for (var i = 2; i < m.length && n > 0; i++){
        return 100;
    }
    return n;
}

// for in
function show(n) {
   var json = {name: 'may'}

   var m = n < 100 || json;

   for(item in m) {
       return 100
   }
   return n
}

// 表达式
function show (n) {
    var m = n >= 100 && 100;
    return m || n;
}
```

# lesson 5 30.38

> 斐波那契数列 (两个数的和等于第三个数) [1,1,2,3,5...]

- 递归
```js
function F(n) {
  if(n<=2) {
      return 1
  }  
  return F(n-1) + F(n-2);

}

console.log(F(8))
```

- 迭代

```js
function F(n) {
    var num1 = 1;
    var num2 = 1;
    var num3 =0;
  for(var i= 0; i < n -2 ;i++) {
    num3 = num1 + num2;
    num1 = num2;
    num2 = num3;
  }

  return num3;
}

console.log(F(8))
```
> 数组排序


- 冒泡排序是两个两个进行比较


如果前面的数>后面的数，进行位置切换。
如果前面的数<后面的数,不进行位置切换。

```
原数据 4，5，1，7，2
第一轮
第一次排序 4，5，1，7，2
第二次排序 4，1，5，7，2
第三次排序 4，1，5，7，2
第四次排序 4，1，5，2，7  找到最大值 
第二轮 ...
```

```js
var arr = [ 4,5,1,7,2];

for (var i = 0; i < arr.length; i++) {
    for (var j = 0; j < arr.length - i; j++) {
        toCon(j, j+1);
    }   
}

function toCon(pre,next){
    var tmp ='';
    if(arr[pre] > arr[next]) {
        tmp = arr[pre];
        arr[pre] = arr[next];
        arr[next] = tmp;     
    }
}

console.log(arr);
```

- 快速排序

 先从数列中取出一个数作为基准数
 区别过程，将比这个数大的书全放到右边，小于或者等于的数放在左边
 再对左右区间重复第二步，知道各区间只有一个数
 
 ```js
 function quickSort(arr) {
    
    if(arr.length <=1) return arr;
    
    var index = Math.floor(arr.length/2);
    
    var pivot = arr.splice(index,1)[0];
    
    var left = [];
    var right = [];
    
    for(var i = 0; i < arr.length; i++) {
        if(arr[i] < pivot) {
            left.push(arr[i])
        } else {
            right.push(arr[i])
        }
    }
    
    return quickSort(left).concat([pivot], quickSort(right))
 }
 ```
 
 
- 简单排序, 每次找到最小值，把最小值放在前面


```js
var arr = [ 4,5,1,7,2];

function sort(arr) {
    if(arr.length === 1) {
        return arr
    }
    var index = 0;
    var iMin = arr[0]
    for (var i = 0 ; i < arr.length; i++ ) {
        if(arr[i] < iMin) {
            iMin = arr[i];
            index = i;
        }
    }

    var pre = arr.splice(index,1);

    return pre.concat(sort(arr))
}


console.log(sort(arr))
```


> 数组去重

```js
var arr = [3,5,2,4,3,5,4,1];

function q(arr) {
  var result = [arr[0]];

  for (var i = 1; i < arr.length; i++) {
      if(toCont(arr[i])) {
          result.push(arr[i])
      }
  }

  function toCont (val) {
      for (var i = 0; i < result.length; i++) {
          if(result[i] === val) {
              return false
          }
      }

      return true;
  }

  return result
}

console.log(q(arr))
```


```js
var arr = [3,5,2,4,3,5,4,1];

function q(arr) {
    var result = [];
    var obj = {};

    for (var i = 0; i < arr.length; i++) {
        if(!obj[arr[i]]) {
            result.push(arr[i]);
            obj[arr[i]] = 1;
      
        }
    }

    return result
    
}

console.log(q(arr))
```

- es6

```js
function unique (arr) {
  const seen = new Map()
  return arr.filter((a) => !seen.has(a) && seen.set(a, 1))
}
// or
function unique (arr) {
  return Array.from(new Set(arr))
}


function unique(arr) {
    return Array.from(new Set(arr))
}
```

# lesson 6 总结自己面试会问到的


> 随机生成指定长度的字符串 比如给定 长度 8  输出 4ldkfg9j

```js
function randomstring(n) {
    var str = 'abcdefghijklmnopqrstuvwxyz9876543210';
    
    var tmp = '';
    for (var i = 0; i < n; i++) {
        tmp += str.charAt(Math.random() * 1);
    }
    return tmp;
}
```

> js打乱数组的最高效方法

```js
var arr = [];
for (var i = 0; i < 100; i++) {
    arr[i] = i;
}

arr.sort(function() {
  return 0.5 - Math.random()
})

var str = arr.join()
```


> Javascript中callee和caller的作用


caller是返回一个对函数的引用，该函数调用了当前函数；

callee是返回正在被执行的function函数，也就是所指定的function对象的正文。


> JSONP原理

可以把需要跨域的请求改成用script脚本加载即可，服务器返回执行字符串，但是这个字符串是在window全局作用域下执行的，你需要把他返回到你的代码的作用域内，这里就需要临时创建一个全局的回调函数，并把到传到后台，最后再整合实际要请求的数组，返回给前端，让浏览器直接调用，用回调的形式回到你的原代码流程中。


> JavaScript事件模型

原始事件模型，捕获型事件模型，冒泡事件模型，
- 原始事件模型就是ele.onclick=function(){}这种类型的事件模型
- 冒泡事件模型是指事件从事件的发生地（目标元素），一直向上传递，直到document 
- 捕获型则恰好相反，事件是从document向下传递，直到事件的发生地（目标元素）

`event.stopPropagation()`
`event.preventDefault` 取消默认时间


> li标签间有空白是怎么回事？ 消除li横排后空隙

需要将其设置为display:inline-block;此时会页面效果是两个<li>之间会有一个大约8px的空白间隙.浏览器的默认行为是把inline元素间的空白字符（空格换行tab）渲染成一个空格

- 既然是因为<li>换行导致的，那就可以将<li>代码全部写在一排
- `.wrap ul{font-size:0px;}`

- `.wrap ul{letter-spacing: -4px;}`

> javascript的本地对象，内置对象和宿主对象

本地对象为array obj regexp等可以new实例化
内置对象为gload Math 等不可以实例化的
宿主为浏览器自带的document,window 等


 > CSS中一个冒号和两个冒号有神马区别？
 
 单冒号(:)用于CSS3伪类，双冒号(::)用于CSS3伪元素。

> 原型对象

每当定义一个对象，对象中都会包含一些预定义的属性，其中函数对象的一个属性就是原型对象 `prototype`

注： 普通对象没有prototype,但是有 `__proto__`

原型底下主要作用是继承

> 原型链

在js创建对象的时候，都有一个 `__proto__`的内置属性，用于指向创建它的函数对象的原型对象的 `prototype`

```js
var person = function(name) {
  this.name = name;
}

person.prototype.getName = function() {
  return this.name
}

  var m = new person('may');
  m.getName(); //may
```


```js

  console.log(m.__proto__ === person.prototype) //true
```

`person.prototype`对象也有`__proto__`属性，它指向创建它的函数对象

```js
  console.log(person.prototype.__proto__ === Object.prototype) //true
```

Object.prototype对象也有__proto__属性，但它比较特殊，为null

```js
console.log(Object.prototype.__proto__) //null
```


> constructor

原型对象 `prototype`中都有个预定义的constructor属性，用来引用它的函数对象，这是一张循环引用

```js
  person.prototype.constructor === person //true
  Function.prototype.constructor === Function //true
  Object.prototype.constructor === Object //true
```

> 作用域

作用域就是变量和函数可以访问的范围，控制着变量和函数的可见性与生命周期

> 作用域链

当代码在一个环境中执行时，会创建变量对象一个作用域，来保证对执行环境有权访问的变量和函数的有序访问

在函数运行过程中标识符的解析是沿着作用域链一级一级搜索的过程，从第一个对象开始，逐级向后回溯，直到找到同名标识符为止，找到后不再继续遍历，找不到就报错。


> html5的新特性

 - 标签语义化，比如header，footer，nav，aside，article，section等，新增了很多表单元素，入email，url等，除去了center等样式标签，还有除去了有性能问题的frame，frameset等标签
 - 音视频元素，video，audio的增加使得我们不需要在依赖外部的插件就可以往网页中加入音视频元素。
 - 新增很多api，比如获取用户地理位置的window.navigator.geoloaction，
 - websocketwebsocket是一种协议，可以让我们建立客户端到服务器端的全双工通信，这就意味着服务器端可以主动推送数据到客户端，
 - webstorage，webstorage是本地存储，存储在客户端，包括localeStorage和sessionStorage，localeStorage是持久化存储在客户端，只要用户不主动删除，就不会消失，sessionStorage也是存储在客户端，但是他的存在时间是一个回话，一旦浏览器的关于该回话的页面关闭了，sessionStorage就消失了，
 - 缓存
 
 - web worker，web worker是运行在浏览器后台的js程序，他不影响主程序的运行，是另开的一个js线程，可以用这个线程执行复杂的数据操作，然后把操作结果通过postMessage传递给主线程，这样在进行复杂且耗时的操作时就不会阻塞主线程了。


> js继承

> http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance.html

第一种方法也是最简单的方法，使用call或apply方法，将父对象的构造函数绑定在子对象上，即在子对象构造函数中加一
```js
function Cat(name,color){
　　　　Animal.apply(this, arguments);
　　　　this.name = name;
　　　　this.color = color;
　　}
　　var cat1 = new Cat("大毛","黄色");
　　alert(cat1.species); // 动物
```
第二种
代码的第一行，我们将Cat的prototype对象指向一个Animal的实例。

原来，任何一个prototype对象都有一个constructor属性，指向它的构造函数。如果没有"Cat.prototype = new Animal();"这一行，Cat.prototype.constructor是指向Cat的；加了这一行以后，Cat.prototype.constructor指向Animal。

这显然会导致继承链的紊乱（cat1明明是用构造函数Cat生成的），因此我们必须手动纠正，将Cat.prototype对象的constructor值改为Cat。这就是第二行的意思。


```js
Cat.prototype = new Animal();
　　Cat.prototype.constructor = Cat;
　　var cat1 = new Cat("大毛","黄色");
　　alert(cat1.species); // 动物
```

第三种
由于Animal对象中，不变的属性都可以直接写入Animal.prototype。所以，我们也可以让Cat()跳过 Animal()，直接继承Animal.prototype。
现在，我们先将Animal对象改写：

```js
function Animal(){ }
　　Animal.prototype.species = "动物";
Cat.prototype = Animal.prototype;
　　Cat.prototype.constructor = Cat;
　　var cat1 = new Cat("大毛","黄色");
　　alert(cat1.species); // 动物
```
> 什么时候需要清浮动，清楚浮动都有哪些方法

通常使用浮动来实现某些元素的布局，但是往往这些元素浮动会影响其他元素的布局，因此会产生副作用。
浮动不再占据文档流的位置，也使浮动元素周围的元素表现的如同浮动元素不存在一样，给布局带来了一些副作用
如果我们不希望要这些效果，就需要清除浮动来解决后患

> 绝对定位与相对定位

相对定位 position: relative; 相对定位是相对于元素在文档中的初始位置
绝对定位 position: absolute; 绝对定位是相对于元素最近的已定位的祖先元素

> 字符串转对象

JSON.parse

> 从对象解析到字符串

json.stringify

> 响应布局

> px 与 rem


> jquery append()方法与html()方法用法区别

> 如果美化一个弹出对话框

> 闭包的用途
一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。
```css
.p {
vertical-align: middle;
}
```

> angularjs作用域

AngularJS中，子作用域一般都会通过JavaScript原型继承机制继承其父作用域的属性和方法。但有一个例外：在directive中使用scope: { ... }，这种方式创建的作用域是一个独立的"Isolate"作用域，它也有父作用域，但父作用域不在其原型链上，不会对父作用域进行原型继承。

> angularjs 指令的封装

> 事件委托

> es6 与之前 相比有什么新特性

> 作用链和作用域

> let const 作用域和 var 有什么区别

> 字符图标的优劣

优点不变形
缺点 纯色

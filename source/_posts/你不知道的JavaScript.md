---
title: 你不知道的JavaScript
date: 2017-05-30 10:47:27
categories: 你不知道的JavaScript
tags: [javascript,notebook,study]
toc: true
---

终于看完，看的很是慢！！！真的很好助眠 ！！！


<!-- more -->

# 第一章 作用域是什么


## 编译原理

传统的编译语言的流程中，程序经历三个步骤


- 分词/词法分析

这个过程将字符组成的字符串分解成有意义的代码块，这些代码块成为词法单元

分词和词法分析之间的差异主要在于词法单元的识别是通过有状态还是无状态的方式进行的，有状态就是词法分析

- 解析/语法分析

将词法单元流转换成一个由元素逐级嵌套所组成的代表了程序语法结构的树

- 代码生成

将AST转换为可执行代码的过程

javascript引擎不会有大量的时间进行优化，与其他语言不通，编译的过程不放生在构建之前，它的引擎用尽办法保证性能

而js的引擎要不比传统的编译复杂，例如还要在语法和代码生成阶段进行优化，包括冗余代码的优化等等,它会在代码执行前对代码进行编译，做好执行的准备

## 理解作用域

- 引擎 从头到尾负责 js程序的编译以及执行过程
- 编译器 引擎的朋友之一，负责词法分析和代码生成等
- 作用域 负责收集并维护有所有声明的标识符组成的一系列查询，确认当前的代码对标识符的访问权限

当看到 `var a = 2` 引擎认为是两个完全不同的声明，一个由编译器在编译时处理，另一个有引擎在运行时处理

编译器会做如下处理 

1 遇到 `var a `,编译器会询问作用域是否已经有同样名称的变量存在同一个作用域的集合中，如果存在则忽略，否则要求作用域在当前作用域的集合中声明一个新的变量，命名为 `a`

2 编译器会为引擎生成所需的代码，这些来代码被用来处理 `a = 2`的赋值操作。引擎运行时会首先询问作用域，当前的作用域集合是否在一个叫做a的变量，如果是，引擎就会使用这个变量，如果否，引擎会继续查找该变量

如果最终找到即赋值，否则跑出异常

> 简单来说

首先编译器会在当前作用域声明一个变量，然后运行时引擎会在该作用域查找变量，如果找到进行赋值操作，没有找到抛出异常

`LHS查询` 当左侧出现变量的赋值操作，试图找到变量的容器本身，从而可以对其赋值

`RHS查询`当右侧出现赋值操作，简单的查找某个变量的值

```js
function foo (a) {
    console.log(a)
}

foo(a);
```
最后一行的`foo`函数调用需要对`foo`进行RHS 
代码中隐式的 `a = 2` 是LHS查询
而`console.log(a)` 中的 `a`进行的是RHS操作

## 作用域嵌套

当一个块或者函数嵌套在另一个块或函数中，就发生了作用域的嵌套，当前作用域无法找到这个变量时，引擎就会在外层嵌套的作用域中继续查找，知道找到该变量或者抵达最外层的作用域为止

## 异常

之所以区分`LHS`和`RHS`
RHS查找无法找到变量的时候，引擎会抛出ReferenceError异常
当引擎执行LHS查找，在非严格模式下，如果没有找到，全局作用域中就会创建一个具有该名称的变量，并将其返回给引擎

# 词法作用域

作用域有两种主要的工作模型
- 普遍的
- 动态作用域

## 词法阶段

`词法化`对源代码进行检查，如果是有状态的解析过程，还会赋予单词语义

`词法作用域`定义在此法阶段的作用域

没有任何函数可以部分地同事出现在两个父级函数中

在查找的过程中，作用域会在找到第一个匹配的标识符时停止，全局变量会自动成为全局对象，可以不直接通过全局对象的词法名称，而是间接的通过对全局对象的属性进行访问`window.a`
但非全局的变量，如果屏蔽了，就无论如何都无法访问到

## 欺骗词法

欺骗词法作用域会导致性能的下降

- eval 插入的代码会修改当前词法作用域的环境，会被当做本来就在那里的一样来处理，eval都可以在运行期修改书写期的词法作用域

但是在严格模式下，运行`eval()`它无法修改词法作用域

```js
function foo(str, a) {
    eval(str);
    console.log(a, b)
}

var b = 2;
foo("var b = 3;", 1); // 1, 3
```


- with 

with通常当做重复引用同一个对象中的多个属性的快捷方式，不需要重复引用对象本身

```js
var obj = {
    a:1,
    b:2,
    c:3
};

obj.a = 2;
obj.b = 3;
obj.c = 4;
```

```js
with(obj) {
    a = 2;
    b = 3;
    c = 4;
}
```

```js
function foo(obj) {
    with(obj) {
        a = 2;
    }
};

var o1 = {
    a: 3
}

var o2 = {
    b:3
};

foo( o1 );
console.log(o1.a); //2

foo(o2);
console.log(o2.a) // undefined
console.log(a); // 2 a倍泄露到全局作用域上了
```

尽管`with`可以将一个没有或者有多个属性的对象处理为一个完全隔离的词法作用域，但是块内部正常的`var`声明并不会被限制在这个块的作用域中，而是被添加到`width`函数所在的作用域中

> 小结

eval函数如果接受了含有一个或者多个代码，就会修改其所在的词法作用域
with声明实际是根据你传递给他的对象凭空创建了一个全系你的词法作用域

- 性能

在编译的时候 如果引擎发现了`eval`或`with`因为无法判断词法分析阶段会受到什么代码，因此之前的性能修改完全没有任何意义


# 函数作用域和块作用域

## 函数中的作用域

属于这个函数的全部变量都可以在整个函数的范围内使用及复用

## 隐藏内部实现
- 私有化
从缩写代码中挑选出任意的片段，然后对函数声明对它进行包装，实际上就把这些代码隐藏起来了
这样将某些方法和属性私有化

避免同名之间的冲突
- 在全局作用域中声明一个足够独特的变量，这个变量通常是个对象，暴露给外界的功能将成为这个属性的对象

- 模块管理 通过依赖管理器的机制将库的标识符显式的导入到另外一个特定的作用域中

## 函数的作用域
在声明一个函数`foo()`意味着foo这个名称本身污染了所在作用域，而且必须显式的通过函数名来调用函数才能运行

- 包装函数，函数会被当做函数表达式而不是一个标准的函数声明来处理

```js
(function () {
    console.log(1);
})();
```

> 区别函数声明和表达式最简单的方法是看function关键字出现在声明中的位置，如果是function是声明的第一个词就是函数声明，否则是表达式

- 匿名和具名

```js
setTimeout(function () {
    console.log('i waited 1 second')
}, 1000);
```

函数表达式中，函数名字可以匿名，但是函数声明中，名字不可忽略

匿名函数的缺点

- 不会显示出有意义的函数名，使得调试很困难
- 当需要引用函数本身时，只能引用 `arguemnts.callee`
- 省略了对代码的可读性

行内函数表达式匿名和具名区别不大，不会有任何影响

- 立即执行函数表达式
```js
(function (a) {
    console.log(a);
})(1)

(function (a) {
    console.log(a)
}(1))
```
由于函数被包含在一对括号内，因此成为一个表达式，通过在末尾加上另外一个()可以立即执行这个函数，第一个括号将函数变成表达式，第二个括号执行这个函数

以上两种写法都可以

## 块作用域

 - with从对象中创建出的作用域仅在with声明中而非外部作用域中有效
 - try/catch 声明变量仅在catch内部有效
 - let 可以将变量绑定到任意所在作用域，使用let不会在块作用域中进行提升
   - 可以清楚的告诉引擎那些变量可以垃圾回收
   - let循环，确保使用上一个循环迭代结束时的值进行重新赋值
   
     > let声明附属一个新的作用域而不是当前的函数作用域
 - const 创建块作用域不安了，其值是固定常量，之后的任何修改都会引起错误
 
 
 > 函数是js中最常见的作用域单元，但函数不是唯一的作用域单元。块作用域指的是变量和函数不仅可以属于所处的作用域，也可以属于某个代码块
 
# 提升
 
## 代码执行顺序
 
```js
 a = 2
 var a;
console.log(a); //2
```
 
 等于
 
```js
 var a;
a = 2;
console.log(a);
```
 
```js
 console.log(a); // undefined
 var a = 2;
```
 
 等于
 
```js
 
 var a;
console.log(a);
a = 2;
```
 
 先有声明，后赋值,也就是说声明本身会被提升，而赋值或者其他逻辑会留在原地
 函数声明会被提升，但函数表达式不会被提升
 
```js
 foo(); // 不是ReferenceError，而是TypeError
 var foo = function bar () {}
```
 
 即使是具名的函数表达式，名称标识符在赋值之前也无法在所在作用域中使用
 
```js
 foo(); //TypeError
 bar(); //ReferenceError
 
 var foo = function bar () {
    
 }
```
 
 等于
 
```js
 var foo;
foo();
bar();
foo = function() {
  
}
```
 
## 函数优先
 
 函数声明和变量声明都会被提升，函数会首先被提升，然后才是变量
 
```js
 foo(); //1
 var foo;
 function foo() {
     console.log(1);
 }
 foo = function () {
     console.log(2)
 }
```
 
尽管 `var foo` 在 `function foo() ..`声明之前，但是是重复声明，函数声明会被提升到普通变量之前

多个函数声明，后者会覆盖前者

```js
foo(); //3

function foo() {
  console.log(1)
}

function foo() {
  console.log(3);
}
```

> 小结

`var a = 2` 当做两个单独的声明，一个是编译极端，一个是执行阶段

声明本身会被提升，而包括函数表达式的赋值内的赋值操作不会提升


# 作用域闭包

## 启示

闭包是基于词法作用域书写代码时所产生的自然结果

## 实质问题

```js
function foo () {
    var a = 2;
    function bar() {
        console.log(a);
    }
    return bar;
}

var bar = foo();

bar(); //2
```

`foo()`执行以后，通常期待 `整个内部作用域被销毁`因为引擎有垃圾回收器来释放不再使用的内存空间。而闭包可以阻止垃圾回收， `bar()`所声明的位置涵盖 `foo()`内部作用域的闭包，使作用域一直存货，以供 `bar()`
在任何时间进行引用

`bar()`对该作用域的引用，这个引用就叫闭包

闭包可以使函数可以继续访问定义时的词法作用域


```js
function foo () {
    var a = 2;
    function baz() {
        console.log(a); //2
    }
    bar (baz)
}

function bar (fn) {
    fn(); // 闭包
}

foo(); //2
```

```js
var fn;

function foo() {
  var a = 2;
  function baz() {
    console.log(a);
  }
  fn = baz; // 将baz分配给全局变量
}

function bar() {
    fn(); // 闭包
}

foo();
bar(); //2
```
无论通过何种手段将内部函数传递到所在的词法作用域外，它都会持有对原始定义作用域的引用，无论在何处执行这个函数都会使用闭包


## 我懂了

```js
function wait(message) {
    
    setTimeout( function timer() {
      console.log(message);
    }, 1000);
}

wait('Hello, closure!') 
```

`wait()`执行1000毫秒后，他的内部作用域并不会消失，函数依然保有wait作用域的闭包

```js
function setupBot(name, selector) {
    $(selector).click(function activator() {
      console.log("activating:" + name);
    })
}

setupBot("Closure Bot 1", "#bot_1");
setupBot("Closure Bot 2", "#bot_2");
```

## 循环和闭包

```js
for (var i = 1; i <= 5; i++) {
    setTimeout( function timer() {
      console.log(i);
    }, i*1000);
}
```

我们希望分别输出1-5，每秒一次
但是实际上代码会以每秒一次的频率输出五次6

> 为什么？
延迟函数的回调会在循环结束时才执行，所有的回调函数依然是在循环结束后才会被执行，因此会每次输出6出来
这段代码的缺陷是我们试图假设循环中每词捕获一个i的副本，但是作用域的工作原理，尽管循环五次在各个迭代中分别定义的，但是他们被密封在一个共享的全局作用域中，实际上只有一个i

- 使用立即执行函数，每次创建新的作用域
```js
for (var i = 0; i < 5; i++) {
    (function(j) {
      setTimeout(function timer() {
          console.log(j);
      }, i*1000)
    })(i)
}
```

- 块作用域，每次创建一个块作用域

```js
for (let i = 0; i < 5; i++) {
    setTimeout(function timer() {
        console.log(i)
    }, i * 1000)
}
```

## 模块


```js
function CoolModule() {
    var something = 'cool';
    var another = [1,2,3];
    function doSomething () {
        console.log(something)
    }
    
    function doAnother() {
      console.log( another.join('!'));
    }
    
    return {
        doSomething: doSomething,
        doAnother: doAnother
    }
}

var foo = CoolModule();
foo.doSomething(); // cool
foo.doAnother(); // 1!2!3
```

- CoolModule是一个函数，必须通过调用来创建一个模块实例，如果不执行外部函数，内部作用域和闭包都无法被创建

- CoolModule返回一个对象字面量语法来表示对象。这个返回的对象中含有对内部函数而不是内部数据的变量引用。保证私有的状态

模块需要具备的两个必要条件

- 必须有外部的封闭函数，函数至少被调用一次

- 封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且访问或者修改私有的状态

只有数据属性而没有闭包函数的对象并不是真正的模块

- 模块函数转为立即执行函数,模块作为普通函数，接受参数

```js
function CoolModule(id) {
    function identify() {
        console.log(id)
    }
    
    return {
        identify: identify
    }
}

var foo1 = CoolModule("foo 1");
var foo2 = CoolModule("foo 2");

foo1.identify(); // "foo 1"
foo2.identify(); // "foo 2"
```

- 

```js

var foo = (function CoolModule(id) {
    function change() {
        publicAPI.identify = identify2;
    }
    
    function identify1() {
        console.log(id);
    }
    
    function identify2() {
        console.log(id.toUpperCase());
    }
    
    var publicAPI = {
        change: change,
        identify: identify1
    }
})("foo module");

foo.identify(); // foo module
foo.change();
foo.identify()// FOO MODULE
```


### 现代的模块机制

```js
var myModule = (function Manger() {
    var modules = {};
    
    function define(name, deps, impl) {
        for (var i = 0; i < deps.length; i++ ) {
            deps[i] = modules[deps[i]];
        }
        modules[name] = impl.apply(impl, deps); // 为模块定义引入了包装函数，将返回值存在一个模块列表中
    }
    
    function get(name) {
        return modules[name];
    }
    
    return {
        define: define,
        get: get
    }
})();


myModule.define('bar',[], function() {
  function hello(who) {
      return 'let me introduce:' + who;
  }
  
  return {
      hello: hello
  }
})

myModule.define('foo', ['bar'], function(bar) {
  var hungry = 'hippo';
  function awesome() {
      console.log(bar.hello(hungry).toUpperCase());
  }
  return {
      awesome: awesome
  }
    
})


var bar = myModule.get('bar');
var foo = myModule.get('foo');

console.log(
    bar.hello('hippo')
) // let me introduce: hippo

foo.awesome(); // LET ME INTRODUCE: HIPPO
```
### 未来的模块机制而是ES6

bar.js
```js
function hello (who) {
    return "let me introduce:" + who;
}

export hello;
```
foo.js
```js
import hello from "bar";

var hungry = 'hippo';
function awesome() {
    console.log( hello(hungry).toUpperCase());
}

export awesome;
```

baz.js
```js
import foo from "foo";
import bar from "bar";

console.log(
    bar.hello("huimin")
) // Let me introduce: huimin

foo.awesome(); //LET ME INTRODUCE: HUIMIN
```

> 小结

当函数可以记住并访问所在的词法作用域，即使函数是在当前词法作用域之外执行，这就产生了闭包

模块的两个主要特征

- 为创建内部作用域而调用一个包装函数
- 包装函数的返回值必须至少包含一个对内部函数的引用，这样就会创建涵盖整个包装函数内部作用域的闭包


# 附录——动态作用域

词法作用域是关于引擎如何寻找变量以及何处找到变量的规则

作用域链是基于调用栈，而不是代码中的作用域嵌套

```js
function foo() {
  console.log(a);
}

function bar() {
  var a = 3;
  foo();
}

var a = 2;

bar(); //2

```
词法作用域实在些代码或者说定义时确定的，而动态作用域实在运行时确定的

# 附录 this词法

箭头函数可以取代当前的词法作用域覆盖了this本来的值

```js
var obj = {
    id: 'cool',
    cool: function coolFn() {
      console.log(this.id);
    }
};

var id = 'not cool';

obj.cool(); // cool

setTimeout(obj.cool, 100); // not cool
```

cool函数丢失了同this之间的绑定

如何解决？

- 箭头函数

- bind


# 关于this


## 误解

- 指向自身

```js
function foo (num) {
    console.log("foo:" + num);
    this.count++;
}

foo.count = 0;

for (var i = 0; i < 10; i++) {
    if(i > 5) {
        foo(i)
    }
}

// foo:6
// foo:7
// foo:8
// foo:9

console.log(foo.count)
// 0
```

由此证明它并不指向自身


解决可以使用foo来代替this

```js
function foo (num) {
    console.log("foo:" + num);
    foo.count++;
}

foo.count = 0;

for (var i = 0; i < 10; i++) {
    if(i > 5) {
        foo(i)
    }
}

// foo:6
// foo:7
// foo:8
// foo:9

console.log(foo.count)
// 0
```

强制this指向foo函数

```js
function foo(num) {
    console.log("foo:" + num);
    
    this.count++;
}

foo.count = 0;


for(var i = 0; i < 10; i++) {
    if(i > 5) {
        foo.call(foo, i);  // 确保this指向函数对象foo本身
    }
}
```

- this指向函数的作用域

this在任何情况下都不指向函数的词法作用域

```js
function foo() {
    var a = 2;
    this.bar();
}

function bar () {
    console.log(this.a)
}

foo() // a is not defined
```

不能使用this来引用一个词法作用域内部的东西


## this是什么

this是在运行时进行绑定的，并不是在编写时绑定的，它的上下文取决于函数调用时的各种条件

> 小结

this机不指向函数自身也不指向函数的词法作用域
this实际上是在函数被调用时发生的绑定，指向什么完全取决于函数在哪里被调用

# this全面解析

## 调用位置

分析调用栈

```js

function baz () {
    // 当前调用栈是： baz 调用位置是全局作用域
    console.log("baz");
    bar()
}

function bar() {
    // 当前调用栈是baz -> bar
    console.log("bar");
    foo();
}

function foo() {
  // 当前调用栈是baz -> bar -> foo
   console.log("foo")
}

baz();
```

## 绑定规则

### 默认绑定

```js
function foo() {
    console.log(this.a);
}

var a = 2;
    
foo(); //2
```

函数调用时默认绑定全局对象，但是如果使用严格模式，全局对象将无法使用默认绑定，报错undefined


### 隐式绑定

调用位置的上下文，或者看是否被某个对象拥有或者包含

```js

function foo () {
    console.log(this.a);
}

var obj = {
    a : 2,
    foo: foo
};

obj.foo(); //2

```

调用位置使用obj的上下文引用函数，是一种包含关系。也是隐式绑定，会把函数调用中的this绑定到obj这个对象上

如果是多层包含关系,引用的是最后一层的

```js
function foo() {
    console.log(this.a);
}

var obj2 = {
    a: 42,
    foo: foo
}

var obj1 = {
    a: 2,
    obj2: obj2
}

obj1.obj2.foo() //42
```

**隐式丢失**

- 函数调用

```js
function foo () {
    console.log(this.a);
}
var obj = {
    a:2,
    foo:foo
}

var bar = obj.foo; // 函数别名
var a = "oops,global"; //a是全局对象
bar();  // "oops,global"
```

bar()是一个函数调用，因此应用默认绑定


- 参数传递

```js
function foo () {
    console.log(this.a);
}

function doFoo(fn) {
    fn(); 
}

var obj = {
    a: 2,
    foo: foo
}

var a = 'oops,global'; //
doFoo(obj.foo) // "oops,global"
```

参数传递就是一种隐式赋值，和上面例子一样

- 传入语言内置的函数

```js
    function foo() {
        console.log(this.a);
    }
    
    var obj = {
        a:2,
        foo: foo
    };
    
    var a = "oops, global"; // a是全局对象的属性
    
    setTimeout(obj.foo, 100); // "oops,global" 
```

### 显示绑定 apply call

```js
function foo() {
    console.log(this.a);
}

var obj = {
    a:2
};

foo.call(obj); //2

```

显示绑定也无法解决绑定丢失的问题

- 硬绑定

```js
function foo () {
    console.log(this.a);
}

var obj = {
    a:2
};

var bar = function () {
    foo.call(obj);
};

bar(); //2
setTimeout(bar, 100); //2
bar.call(window);  //2
```
硬是绑定不可以在修改其他this

> 应用场景

创建一个包装函数，传入所有的参数并返回接收到的所有制

```js
function foo(something) {
    console.log( this.a, something );
    return this.a + something;
}
var obj = {
    a:2
};
var bar = function() {
    return foo.apply( obj, arguments );
};
var b = bar( 3 ); // 2 3
console.log( b ); // 5
```

创建一个可重复使用的辅助函数

```js
function foo (something) {
    console.log(this.a, something);
    return this.a + something;
}

function bind(fn, obj) {
    return function () {
        return fn.apply(obj, arguments)
    }
}

var obj = {
    a:2
}

var bar = bind(foo, obj);

var b = bar(3); //2 3

console.log(b); //5
```

ES5中内置了bind方法

```js
function foo(something) {
console.log( this.a, something );
return this.a + something;
}

var obj = {
    a:2
};

var bar = foo.bind(obj);

var b = bar(3); //2 3

console.log(b) // 5
```


foreach

```js
function foo (el) {
    console.log(el, this.id)
}

var obj = {
    id: 'awesome'
};

[1,2,3].forEach(foo, obj);

// 1 awesome 2 awesome 3 awesome
```

### new绑定

构造函数只是一些使用new操作符时被调用的函数，不属于某个类，也不会实例化一个类

使用new来调用函数，会执行以下操作

- 创造一个全新的对象
- 新对象会执行 `[[原型]]` 连接
- 这个新对象会绑定到函数调用this
- 如果函数没有返回其他对象，那么new表达式中的函数会自动返回这个新对象


```js
function foo(a) {
    this.a = a;
}

var bar = new foo(2);
console.log(bar.a); //2
```

使用new调用foo,会构造一个新对象将其绑定在foo的this上


## 优先级

默认的绑定优先级最低

显示绑定 > 隐式绑定

new绑定 > 隐式绑定

> 判断this

1 函数是否在new中调用? 如果是 this的绑定是新创建的对象
2 函数是否通过 call apply调用? 如果是this绑定制定对象
3 函数是否在某个上下文对象中调用?如果是就是this绑定的上下文对象
4 如果都不是某人绑定，严格模式undefined

## 绑定例外

### 被忽略的this

如果把null或undefined作为this的绑定对象闯入call、apply、bind.调用会被忽略

null 常用来展开一个数组，并当做参数传入一个函数

```js
function foo(a, b) {
    console.log( "a:" + a + ", b:" + b)
} 

foo.apply(null, [2, 3]); //a:2. b:3
var bar = foo.bind(null, 2);

bar(3); // a:2, b:3
```

也可以用一个特殊对象

```js
function foo(a,b) {
console.log( "a:" + a + ", b:" + b );
} // 我们的 DMZ 空对象
var ø = Object.create( null );
// 把数组展开成参数
foo.apply( ø, [2, 3] ); // a:2, b:3
// 使用 bind(..) 进行柯里化
var bar = foo.bind( ø, 2 );
bar( 3 ); // a:2, b:3
```

### 间接引用

无意间创建函数的间接引用，这种情况下，调用这个函数会应用默认绑定规则

```js
function foo () {
    console.log(this.a);
}

var a = 2;
var o = {a: 3, foo: foo};
var p = {a: 4};

o.foo(); //3
(p.foo = o.foo)() // 2
```

`p.foo = o.foo`的返回值是目标函数的引用，调用这个函数会默认绑定规则

### 软绑定

```js
if(!Function .prototype.softBind) {
    Function.prototype.softBind = function (obj) {
        var fn = this;
        var curried = [].slice.call(arguments,1);
        var bound = function () {
            return fn.apply(
            (!this || this === (window || global)) ? obj : this, curried.concat.apply(curried, arguments));   
        }
            bound.prototype = Object.create( fn.prototype);
    }
}
```
 
 ```js
 function foo () {
    console.log("name" + this.name);
 }
 
 var obj = {name: 'obj'},
     obj2 = {name: 'obj2'},
     obj3 = {name: 'obj3'};

var fooOBJ = foo.softBind(obj);
fooOBJ(); //name: obj


obj2.foo = foo.softBind(obj)
obj2.foo(); //name: obj2

fooOBJ.call(obj3); //name:obj3 

setTimeout(obj2.foo, 10);  //name: obj
 ```
 
 ## this 词法
 
 箭头函数
 
 ```js
 function foo () {
    return (a) => {
        console.log(this.a);
    }
 }
 
 var obj1 = {
    a: 2
 }
 
 var obj2 = {
    a: 3
}

var bar = foo.call(obj1);

bar.call(obj2); //2

 ```
 
 箭头函数的绑定无法被修改
 
 常用于回调函数
 
 ```js
 function foo() {
    setTimeout(() => {
        console.log(this.a)    
    }, 100)
 }
 
 var obj = {
    a:2
 };

foo.call(obj); //2
 ```
 
 > 小结
 
 this绑定，需要找到函数的直接调用位置
 
 1 new是否调用  绑定到新创建的对象
 2 是否用call apply  绑定到指定对象
 3 上下文调用
 4 默认
 
 箭头函数不会使用四条标准绑定规则，而是根据当前的词法作用域来决定this绑定
 
 # 对象
 
 ## 语法
 
```js
var myObj = {
    key: value
};
```
```js
var myObj = new Object();
myObj.key = value;
```

## 类型

六中类型

- string
- number
- boolean
- null
- undefined
- object

除object外都是简单基本类型
js中并不是万物皆对象

js中还有一些对象是内置对象

- string
- number
- boolean
- object
- function 
- array
- Date
- RegExp
- Error

typeof是一个一元运算，放在一个运算数之前，运算数可以是任何类型

instanceof 用于判断一个变量是否是某个对象的实例

```js
var str = "hello";
typeof str; // "string"
str instanceof String; // false
```

```js
var strObj = new String("I am a string");
typeof strObject; // object
strObj instanceof String; // true
```

## 内容

```js
var myObject = {
    a: 2
}

myObject.a; //2
myObject["a"]; //2
```

a语法通常被称为 **属性访问**
['a'] 语法通常被称为 **键访问**
这两周语法的区别在于，操作符属性满足标识符的命名规则，而键访问可以使用不是有效的标识符属性名

在对象中，属性名永远都是字符串
### 可计算属性名

```js
var prefix = 'foo';
var myObject = {
    [prefix + 'bar']: hello,
    [prefix + 'baz']: 'world'
}

myObject['foobar']; // hello
myObject['foobaz']; // world

```

### 属性与方法

函数永远不会属于一个对象，对象内部的函数成为方法是不妥的，函数和对象的关系是间接关系


从属性中引用的函数会this被隐式绑定到一个对象


### 数组

给数组增加命名属性，数组的length值并未发生变化
```js
var myArr = ['foo', 42, 'bar'];
myArr.baz = 'bar';
myArr.length; //3
myArr.baz; // bar
```
但如果用一个数字，那么是字符串数字做下标，它会修改数组

```js
var myArr = ['foo', 42, 'bar'];
myarr['3'] = 'baz';
myArr.length; //4
myArr[3]; //baz
```

### 复制对象

首先我们应该考虑是浅复制还是深复制

浅复制可以考虑es6的assign

```js
var myObject = {
a: 2
};

var newObject = Object.assign({},myObject);

myObject.a = 3;

console.log(newObject.a); //2
```

### 属性描述符

```js
var myObject = {
    a:2
};

console.log(Object.getOwnPropertyDescriptor(myObject, 'a'));

// {
// value: 2,
// writable: true,
// enumerable: true,
// confgurable: true
// }
```

writable(可写) enumerable(可枚举) configurable(可配置)

在创建普通属性时描述符会使用默认值，也可以使用object.defineProperty（）
来增加或者修改一个已有属性


```js
var myObject = {};
Object.defineProperty(myObject, 'a', {
    value: 2,
    writable: true,
    configurable: true,
    enumerable: true
});

myObject.a; //2
```

- writable 决定是否可以修改属性的值

- configurable 决定属性是否可配置，是否可以使用defineProperty()方法来修改属性描述符，如果第一改成false ，后面再改成true会报错

它是单向操作，无法撤销

> 即使configurable为false它的值还是可以修改的，但是writable的状态由true改为false，但是无法false改为true，还会禁止删除这个属性。

- enumerable 可枚举，对象属性的枚举比如 如果设置成false ,属性就不会出现在枚举中，设置成true 就会让这个属性出现在枚举中。



### 不变性

- 对象常量

var myObject = {};

Object.defineProperty( myObject, 'FAVORITE', {
    value: 42,
    writable: false,
    configurable: false
})

- 禁止扩展

禁止添加新属性并且保留已有的属性

Object.prevent.Extensions(...);

```js
var myObject = {
    a: 2
}

Object.preventExtensions(myObject);

myObject.b = 3;
myObject.b; //undefined
```

- 密封

Object.seal(...) 会创建一个密封的对象，实际会在一个现有对象上调用Object.preventExtensions()并把所有现有属性标记为configurable：false

密封之后不仅不能添加新属性，也不能重新配置或者删除任何现有的属性

- 冻结

Object.freeze（...） 会创建一个冻结对象，这个方法实际上会调用Object.seal(...)并把所有数据访问标记为writable:false,这样就无法修改他们的值

### get

一次属性的访问，实际上就是实现了get操作，如果找到就返回该值，如果没有找到就返回undefined

### put

给对象的属性赋值会触发put来设置或者创建这个属性

put操作还会检查

- 是否访问描述符？ 如果是且存在就调用setter
- writable是否是false? 如果是，非严格模式失败，严格模式爆异常
- 如果都不是，将该值设置为属性的值

### getter和setter

对象默认的put和get操作可以控制属性的设置和获取


```js
var myObject = {
    get a() {
        return 2;
    }
}

myObject.a = 3;

console.log(myObject.a) //2
```

既然有合法的setter,由于我们定义的getter只会返回2，所以set操作是没有意义的。

setter会覆盖单个属性的默认操作

```js
var myObject = {
    get a() {
        return this._a;
    },
    set a(val) {
        this._a = val * 2;
    }
}

myObject.a = 2;

console.log(myObject.a) //4
```

### 存在性

如何区分属性中存的undefined和属性不存在的undefined？

```js
var myObject = {
    a:2
};

('a' in myObject) //true
('b' in myObject) //false

myObject.hasOwnProperty('a'); //true
myObject.hasOwnProperty('b'); //false


```

- in 操作符会检查属性是否在对象以及原型链中
- hasOwnProperty只会检查属性是否在myObject对象中，不会检查原型链

```js
Object.prototype.hasOwnProperty.call(myObject, 'a');
```

```js
Object.propertyIsEnumerable('a'); //true
Object.propertyIsEnumerable('b'); //false

Object.keys(myObject); // ['a']
Object.getOwnPropertyNames(myObject); // ['a', 'b']
```


- propertyIsEnumerable() // 判断枚举性 仅满足enumerable: true

- object.keys 会返回一个数组，包含所有可枚举属性

- object.getOwnPropertyNames() 返回一个数组，包含所有属性无论是否可枚举

## 遍历

for...in 实际遍历的是下标来指向值,遍历可枚举的的对象

所以增加了 forEach every some

forEach 会遍历数组中所有值饼忽略回调函数的返回值

every会一直遍历除非回调返回false

some会运行到回调函数返回true

for...of 遍历数组直接遍历值而不是数组下标，如果不是数组需要自定义@@iterator对象并调用next()方法来遍历数据值。

```js
varmyArray = [1,2,3];

for ( var v of myArray) {
    console.log(v);
}

// 1
// 2
// 3

```

# 混合对象类

## 类理论

js只有一些近似类的语法，在软件设计中类是一种可选的模式

## 类的机制


## 类的继承

先定义一个类，然后定义一个继承前者的类
后者成为子类，前者是父类

- 多态

子类重写了继承自父类的方法，但是重写的方法里面调用了父类的方法，这种技术叫做**多态或者虚拟多态或者相对多态** 

```js
class Vehicle {
    engines = 1
    drive() {
        ignition();
        output('lalalala');
    }
}

class Car inherites Vehicle {
    wheels = 4;
    dirve() {
        inherited:drive()
        output('lalallal222222')
    }
}
```

子类对集成到的一个方法的重写不会影响父类中的方法
子类的导师父类的一份副本，
类的继承其实就是复制

- 多重继承

js 没有多重继承的功能，但是很多人为了实现多重继承，会用到混入

## 混入

js开发者模拟类的复制行为，这个方法就是混入分显示和隐式


### 显示混入

遍历sourceObj 如果targetObj没有这个属性就会进行复制

```js
function mixin (sourceObj, targetObj) {
    for (var key in sourceObj) {
        if(!(key in targetObj)) {
            target[key] = sourceObj[key];
        }
    }
    return targetObj;
}

var Vechicle = {
    engines: 1,
    igntion: function() {
      console.log('Turning on my engine');
    },
    drive: function () {
        this.ignition();
        console.log('Steering and moving forward!')
    }
}

var Car = mixin(Vechicle, {
    wheels: 4,
    dirve: function () {
        Vechicle.drive.call(this);  // 显示多态
        console.log('Rolling on all' + this.wheels + 'wheels!')
    }
})
```

- 多态的引入会极大的增加维护成本，此外，显示伪多态可以模拟多重继承，会进一步增加代码的复杂度和维护度

- 混合复制

如果mixin不判断targetObj有没有这个属性，都会复制到targetObj对象里面，会不小心覆盖目标对象原有属性

```js
function mixin(sourceObj, targetObj) {
    for (var key in sourceObj) {
        targetObj[key] = sourceObj[key];
    }
    
    return targetObj
}


var Vehicle = {};

var Car = mixin(Vehicle, {});

mixin( {
    wheels: 4,
    dirive; function () {}
}, car)
```

只有在能够提高代码可读性的前提下使用显示混入，避免使用增加代码理解难度

- 寄生继承

复制一份Vehicle父类的对象，然后混入子类对象的定义，然后使用这个符合对象的构建实例

```js
function Vehicle() {
    this.engines = 1;
}

Vehicle.prototype.ignition = function () {
    console.log(111);
}

Vehicle.prototype.drive = function () {
    this.ignition();
    console.log('2222');
}


function Car () {
    var car = new Vehicle(); // 复制
    car.wheels = 4;
    var vehDrive = car.drive;
    car.drive = function () {
        vehDrive.call(this);
        console.log('3333333333333');
    }
    
    return car;
}

var myCar = new Car();

```

### 隐式混合

```js
var Something = {
    cool:function () {
        this.greeting = 'hello world';
        this.count = this.count ? this.count + 1 : 1;
    }
};

Something.cool();
Something.greeting; // 'Hello World'
Something.count; //1

var Another = {
    cool: function () {
        Something.cool.call(this);  // 隐式混入
    }
}

Another.cool();
Another.greeting; // 'Hello World'
Another.count; //1 不是共享状态
```

> 小结

类意味复制

类被继承时，行为也会被复制到子类中

看起来是从子类引用到父类，但是本质上引用的其实是复制的结果

混入模式可以用来模拟复制行为，但会产生脆弱丑陋的语法

显示混入无法完全模拟类的复制行为

# 原型

## prototype

所有的对象在创建时 `prototype`属性都会被赋予一个非空的值

如果无法在对象本身找到需要的属性，就会继续访问对象的 `prototype`链

for ... in 遍历对象原理和查找prototype链类似，任何可以通过原型链访问到的属性都会被枚举

```js
var antherObject = {
    a: 2
}

var myObject = Object.create(antherObject);
for ( var k in myObject) {
    console.log('food:' + k);
}
// found: a
```

### object.prototype

所有的普通 `prototype`链注重都会指向内置的Object.prototype

### 属性设置和屏蔽

```js
myOnject.foo = 'bar'
```

- 如果在 `prototype`链的上层存在名为foo的普通数据访问属性没有被标记制度，那么会在myObject中添加一个名为foo的心属性，他是屏蔽属性
- 如果 `prototype`链上层存在foo, 被标记为只读（writable: false）那么无法修改已有属性或者myObject
- 如果 `prototype` 链上层存在foo并且它是一个setter,那就一定会调用这个setter. foo不会被添加到myObject，也不会重新定义foo这个setter

当遇到二三情况的时候可以使用Object.defineProperty来向myObject添加foo

只读false属性会阻止链下层隐式创建同名属性，这样主要为了模拟类属性的继承

```js
var anotherObject = {
    a: 2
};

var myObject = Object.create( anotherObject );
anotherObject.a; //2
myObject.a; //2

anotherObject.hasOwnProperty('a'); //true;
myObject.hasOwnProperty('a'); //false

myObject.a++; //隐式屏蔽
anotherObject.a; //2
myObject.a //3

myObject.hasOwnProperty('a'); //true
```

## 类

js中只有对象

### '类'函数

Object.getPrototype(object) //返回对象的原型

```js
function Foo() {}

var a = new Foo(); // a内部的prototype 关联到Foo.prototype指向的对象

Object.getPrototypeOf(a) === Foo.prototype; //true

```

new并没有直接创建关联，它只是关联到其他对象的新对象

继承意味着复制操作，js并不会复制对象属性，而是创建一个关联

### 构造函数

```js
function Foo () {
    
}

Foo.prototype.constructor === Foo; //true

var a = new Foo();
a.constructor === Foo //true
```

`constructor`指向构造他的函数

new 后面第一个首字母大写

### 技术

Foo.prototype的.constructor属性时Foo函数在声明时的默认属性，如果你创建一个新对象并替换了函数默认的prototype对象引用，那么新对象并不会自动获得constructor属性

```js
function Foo() {}
Foo.prototype = {};

var a1 = new Foo ();
a1.constructor === Foo; //false
a1.constructor === Object; // true
```

a1 没有 constructor 属性，所以会委托 `prototype`上的Foo.prototype 但是它也没有constructor属性，继续委托，委托给顶端的Object.prototype

而修复constructor需要很多手动操作 它是一个不可枚举的属性

## （原型）继承

如何把Bar.prototype 关联到Foo.prototype
//需要抛弃Bar原有的prototype
Bar.prototype = Object.create(Foo.prototype);
//es6可以直接修改现有的Bar.prototype
Object.setPrototypeOf(Bar.prototype, Foo.prototype);

- 检查类的关系



```js
function Foo() {
    
}

Foo.prototype.blah = ...;

var a = new Foo();
```

> 对象a，如何找到a委托的对象

- instanceof

```js
a instanceof Foo; //true
```

如果内置的.bind()函数来生成一个硬绑定函数，该函数没有prototype属性，函数使用instanceof,目标函数prototype会代替硬绑定的prototype

- 反射

```js
Foo.prototype.isPrototypeOf(a); // true
```

a的整条prototype链中是否出现过Foo.prototype

也可以直接获取一个对象prototype链

Object.getPrototypeOf(a);

Object.getPrototype(a) === Foo.prototype; //true

a._proto_ === Foo.prototype; //true


constructor和_proto_并不存在于珍重的使用对象中，而是内置在Object.prototype中

## 对象关联

### 创建关联

Object.create 会创建一个新对象并把它关联到我们指定的对象

Object.create(null) 会常见一个拥有空链接的对象，对象无法委托

### 关联关系是备用

假如想调用myObject.cool但是myObject中不存在cool
```js
var anotherObject = {
    cool: function () {
        console.log('cool')
    }
}

var myObject = Object.create(anotherObject);

myObject.doCool = function () {
    this.cool(); //内部委托
}

myObject.doCool(); // cool
```

> 小结

如果要访问对象中不存在的属性，就会查找对象内部 `prototype`关联的对象

js机制的核心不会复制，对象之间通过内部 `prototype`链关联


# 行为委托

## 面向委托的设计

类设计模式鼓励继承时使用方法重写和多态

### 委托理论

```js
Task = {
    setID: function (ID) {this.id = ID;},
    outputID: function () {
      console.log(this.id)
    };
    
    XYZ = Object.create(Task);
    XYZ.prepareTask = function (ID, Label) {
        this.setId(ID);
        this.lebel = Label;
    };
    
    XYZ.outputTaskDetaild = function () {
        this.outputId();
        console.log(this.label);
    };
}
```

关联代码的不同之处

- id和lablel数据成员都是直接存在XYZ上，而不是Task(委托目标)
- 在类的设计中故意让父类和子类都有outputTask这样可以利用重写的优势，而委托中应避免在不同级别中使用相同的命名
- XYZ中的方法首先会寻找XYZ自身是否有setID,如果没哟，会通过委托关联到Task继续寻找

委托行为意味着XYZ找不到属性或者方法引用时会吧这个请求委托给另一个对象Task

委托最好在内部实现，不要直接暴露，调用XYZ.prepareTask()

- 相互委托禁止，会出错

- 调用

```js
function Foo () {}

var a1 = new Foo();
a1; // Foo{} chrome  Object{} firefox
```

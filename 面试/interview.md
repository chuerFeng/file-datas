# JavaScript基础



## Chome版本

+ 金丝雀
+ 开发版
+ 测试版
+ 正式版



## Http缓存策略

### 目的

为了减少服务器压力，减少对服务器的请求，提升网站性能。

### 定义

确定Http响应是否可以被客户端缓存，以及哪些可以被缓存

### 流程

![](https://raw.githubusercontent.com/chuerFeng/pictureBed/master/img/16557061-cb417c9b7d3883bd.jpg)





http请求时，会检测是否有本地缓存，如果本地命中了缓存会优先从本地取，如果没有本地缓存则浏览器再向服务器请求*(虽然本地可能有缓存，但却不一定会使用本地缓存，因为本地缓存可能是已经过期了的，为了鉴别是否过期就需要后面两种缓存规则结合)*，而http向服务器请求缓存规则分为两类： **强缓存**， **协商缓存(对比缓存)**。

#### 强缓存

直接从缓存数据库里取，无需请求服务器

![](https://raw.githubusercontent.com/chuerFeng/pictureBed/master/img/16557061-3b1a410230ecb1af.jpg)

用来判断是否命中强缓存的字段为`Expires` 和 `Cache-control`，`cache-control`优先于`Expires`，而这两个字段只是标记缓存是否过期，如果过期还需重新发送请求资源。

##### Expires

> HTTP1.0的产物，现已过期

Expires返回绝对时间GMT格式字符串，如果客户端本地时间超过该时间就表示已经过期，需要重新请求

**缺点**： 时间可能不准确，客户端需要同步时间



##### Cache-control常用字段

**public**： 表示任何资源都缓存

**no-cache**： 表示每次使用本地缓存前都要向服务器请求(需要协商缓存)，当服务器允许才使用本地缓存

**private**： 表示响应的结果只能被单个用户缓存

**no-store**: 表示禁止缓存

**max-age**: 表示缓存的时间，指我们的web中的文件被用户访问(请求)后的存活时间,是个相对的值,相对Request_time(请求时间).







## JavaScript



### 函数表达式和函数声明

ECMA规范明确了一点：函数声明必须带有标示符（Identifier）（函数名称），而函数表达式则可以省略这个标示符：

　函数声明:        `function 函数名称 (参数：可选){ 函数体 }`

　函数表达式： `function 函数名称（可选）(参数：可选){ 函数体 }`



如果不声明函数的名称，那一定就是函数表达式；

如果声明了函数名称 那就要看代码在`执行上下文中`是否是作为`赋值表达式`的一部分，如果是则是`函数表达式`，如果function foo(){}被包含在一个函数体内，或者位于程序的最顶部的话，那它就是一个函数声明。

```javascript
 function foo(){} // 声明，因为它是程序的一部分
 var bar = function foo(){}; // 表达式，因为它是赋值表达式的一部分

 new function bar(){}; // 表达式，因为它是new表达式

 (function(){
   function bar(){} // 声明，因为它是函数体的一部分
 })();
```





表达式和声明存在着十分微妙的差别，首先，函数声明会在任何表达式被解析和求值之前先被解析和求值，即使你的声明在代码的最后一行，它也会在同作用域内第一个表达式之前被解析/求值，参考如下例子，函数fn是在alert之后声明的，但是在alert执行的时候，fn已经有定义了：

```
  alert(fn());

  function fn() {
    return 'Hello world!';
  }
```



#### 命名函数表达式

`var bar = function foo(){}`  就是一个有效的命名函数表达式，但有一点需要记住：这个名字只在新定义的函数作用域内有效，因为规范规定了标示符不能在外围的作用域内有效：

```
 var f = function foo(){
    return typeof foo; // foo是在内部作用域内有效
  };
  // foo在外部用于是不可见的
  typeof foo; // "undefined"
  f(); // "function"
```

命名函数表达式的作用是方便调试，在调用栈中的每个项都有自己的名字来描述，那调试器在调试的时候会将它的名字显示在调用的栈上









### 执行上下文/作用域链/闭包



#### 作用域

作用域是指程序源代码中定义变量的区域

作用域决定了去哪查找变量，即当前执行环境的代码对变量访问的权限

JavaScript 采用`词法作用域(lexical scoping)`，也就是`静态作用域`。



##### 静态作用域

 JavaScript 采用的是`词法作用域`，`函数的作用域`在函数定义的时候就决定了。

当执行一个函数时，会先从该函数内部去查找有无`目标变量`，如果没找到就从定义函数那一层开始往外找，直到到达全局上下文。

##### 动态作用域

动态作用域，函数的作用域是在函数调用的时候才决定。



##### 作用域链

当查找变量的时候，会先从当前上下文的变量对象中查找，如果没有找到，就会从父级(词法层面上的父级)执行上下文的变量对象中查找，一直找到全局上下文的变量对象，也就是全局对象。这样由多个执行上下文的变量对象构成的链表就叫做作用域链。



#####  [[scope]]

函数的作用域之所以在定义时就决定了是因为   函数有一个`[[scope]]`内部属性，当函数创建时会在该属性中保存所有父变量对象，就是所有父对象的层级链，但是注意：[[scope]] 并不代表完整的作用域链！





#### 执行上下文 

执行上下文就是当前 JavaScript 代码被解析和执行时所在环境的抽象概念， JavaScript 中运行任何的代码都是在执行上下文中运行。**执行上下文**(Execution Context)可以看成是做事情前的**准备工作**。



执行上下文有两个阶段：

[创建阶段](#createec)

执行阶段

执行上下文环境三种类型:   

- 全局执行上下文
- 函数执行上下文
- Eval执行上下文

每个执行上下文中都有三个重要的属性:

- 变量对象(Variable object，VO)
- 作用域链(Scope chain)
- this



##### <span id="createec">创建阶段</span >

创建执行上下文阶段有三个重要的过程：

- 修改this指向
- 创建词法环境  
- 创建变量环境



1. 修改this指向

2. 创建词法环境

   JavaScript采用的是**词法作用域(静态作用域)**，函数的作用域在函数定义的时候就已经决定了。

   词法环境是Js引擎内部用来跟踪**标识符**和**特定变量**之间的映射关系，词法环境是Js作用域的实现机制，通常又被称为**作用域**；**值得注意的是，Js的词法环境在其创建的时候就已经决定了**。

   函数在定义时的环境与调用函数的环境往往是不相同的，无论何时调用函数，都会创建一个新的执行环境， 被推入执行上下文栈。并且还会创建一个与之相关联的词法环境,Js引擎将调用函数的内置`[[Environment]]`属性与创建函数时的环境进行关联。在其原来的词法环境中若找不到值，则会向其外部环境中查询。

   > https://blog.csdn.net/qq_31594099/article/details/83003516

简单的用两个例子来理词法环境



![](https://raw.githubusercontent.com/chuerFeng/pictureBed/master/img/20210910172809.png)

无论何时创建函数，都会创建一个与之想关联的词法环境，并存储在名为`[[Environment]]`的内部属性上，在上面示例中bar函数保存了全局环境的引用,foo函数保存了bar函数的引用。所以为2



![](https://raw.githubusercontent.com/chuerFeng/pictureBed/master/img/20210910173512.png)

foo调用时的词法环境内为`console.log(a)`，Js引擎会将其与其创建时的环境（即全局环境）进行关联。当其内部找不到a时，则会在全局环境中去找a，而不是bar函数的环境。所以结果是2

 3. 创建变量环境

    **变量环境**也是一个词法环境，所以它有着**词法环境**的所有属性。

    跟**词法环境**作用一样，调用时都会创建一个执行上下文推入调用栈

    

***词法环境***和***变量环境***的不同

- `变量环境`：通过`var`声明或`function(){}`声明的变量存在这里
- `词法环境`：通过`let const with() try-catch`创建的变量存在这里



```
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f();
}
checkscope();
```

```
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f;
}
checkscope()();
```

都是输出`local scope`

原因JavaScript采用的是词法作用域，函数的作用域基于函数创建的位置



##### 执行阶段

[上下文执行阶段]: https://www.cnblogs.com/TomXu/archive/2012/01/16/2309728.html

执行上下文的代码会分成两个阶段：

1.   进入执行上下文
2.   执行代码	

###### 进入执行上下文

VO对象包含下列属性

1. 函数的所有形参

   由名称和对应值组成一个变量对象，如没有值则赋值underfined

2. 所有的函数声明

   由名称和对应值(函数对象)组成一个变量对象，如果有相同变量名称的属性，则完全覆盖

3. 所有的变量声明

   由名称和对应值(函数对象)组成一个变量对象，如果变量名称跟已经声明的形式参数或函数相同，则变量声明不会干扰已经存在的这类属性。



看一个例子：

```
function test(a, b) {
  var c = 10;
  function d() {}
  var e = function _e() {};
  (function x() {});
}
 
test(10); // call
```

当进入带有参数10的test函数上下文时，AO表现为如下：

```
AO(test) = {
  a: 10,
  b: undefined,
  c: undefined,
  d: <reference to FunctionDeclaration "d">
  e: undefined
};
```

注意，AO里并不包含函数“x”。这是因为“x” 是一个函数表达式(FunctionExpression, 缩写为 FE) 而不是函数声明，函数表达式不会影响VO。 不管怎样，函数“_e” 同样也是函数表达式，但是就像我们下面将看到的那样，因为它分配给了变量 “e”，所以它可以通过名称“e”来访问。 函数声明FunctionDeclaration与函数表达式FunctionExpression 的不同。







#### **执行上下文栈**

又称*调用栈*，LIFO数据结构栈(后进先出规则)，是JavaScript引擎为了管理众多的执行上下文而创建的一个**执行上下文栈(ECStack)**。

执行上下文栈的进出规则：

1. js初始化时会向执行栈中压入**全局执行上下文(globalContext)**，当执行栈被清空前永远都有**全局执行上下文**在最底部
2. 当执行一个函数时会创建一个**函数执行上下文**，并压入执行栈。当函数执行完时**函数执行上下文**会从栈中弹出。

```javascript
function fun3() {
    console.log('fun3')
}
function fun2() {
    fun3();
}
function fun1() {
    fun2();
}
fun1();
```

执行栈压入顺序

```javascript
// 伪代码

// fun1()
ECStack.push(<fun1> functionContext);

// fun1中竟然调用了fun2，还要创建fun2的执行上下文
ECStack.push(<fun2> functionContext);

// 擦，fun2还调用了fun3！
ECStack.push(<fun3> functionContext);

// fun3执行完毕
ECStack.pop();

// fun2执行完毕
ECStack.pop();

// fun1执行完毕
ECStack.pop();

// javascript接着执行下面的代码，但是ECStack底层永远有个globalContex
```





1. ~~当浏览器执行JavaScript时，js引擎会创建一个**全局执行上下文**并把该上下文推入调用栈。~~
2. ~~当调用一个函数first()时，js引擎会为该函数创建一个**函数执行上下文**并推入调用栈。~~
3. ~~当在first()函数内部调用另一个函数second()时，js引擎创建一个新的**函数执行上下文**并推入调用栈。~~
4. ~~当second()执行完成后调用栈将**second的上下文**弹出，控制流程到达下一个执行上下文，即**first函数执行上下文**~~
5. ~~first执行完毕时，到达**全局执行上下文**~~
6. ~~所有代码执行完毕时，**全局执行上下文**被移出调用栈，调用栈空~~







#### **变量对象**

[JavaScript深入之变量对象]: https://github.com/mqyqingfeng/Blog/issues/5

变量对象(Variable object，VO)是与执行上下文相关的数据作用域，存储了在上下文中定义的变量和函数声明， VO对象只是一个抽象概念，由于VO所在的执行上下文环境不一样初始的数据也不一样。



##### 在全局上下文中

在客户端中，全局对象就是 Window 对象，而全局对象是由Object构造函数实例化的一个对象。

在实例化过程中会给全局对象赋值了一些预定义函数和属性，如 `Math`, `Date`

全局上下文中的`变量对象`其实就是`全局对象`



##### 在函数上下文中

在函数上下文中，通常用活动对象(activation object, OA对象)来表示变量对象，也可以看成活动对象是变量对象的特例，因为它包含了变量对象的的所有属性和变量定义，函数声明。

###### 活动对象(OA对象)

在进入函数上下文前， 函数上下文中的VO是不能直接访问的（通常用`活动对象(OA对象)`来表示`变量对象`），只有当进入了`执行上下文`后该上下文的变量才会被激活，而被激活后的`变量对象`就是`活动对象`，该对象的属性也才能被访问。

简而言之，活动对象就是在进入函数上下文时被激活创建的，这个对象通过函数的arguments属性初始化，arguments属性的值是Arguments对象，Arguments对象是活动对象的一个属性，该属性包括如下属性

1. callee  -- 指向当前函数的引用
2. length -- 真正传递的参数个数
3. properties-indexes (字符串类型的整数) 属性的值就是函数的参数值(按参数列表从左到右排列)。 properties-indexes内部元素的个数等于arguments.length。properties-indexes 的值和实际传递进来的参数之间是共享的。











### 宏任务和微任务

> ES6 规范中，microtask 称为 `jobs`，macrotask 称为 `task`
> 宏任务是由宿主发起的，而微任务由JavaScript自身发起。

|                    | 宏任务（macrotask）                                          | 微任务（microtask）                                          |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 谁发起的           | 宿主（Node、浏览器）                                         | JS引擎                                                       |
| 具体事件           | 1. script (可以理解为外层同步代码) <br />2. setTimeout/setInterval <br />3. UI rendering/UI事件 <br />4. postMessage，MessageChannel <br />5. setImmediate，I/O（Node.js） | 1. Promise<br/>2. MutaionObserver<br/>3. Object.observe（已废弃；`Proxy` 对象替代）<br/>4. process.nextTick（Node.js） |
| 谁先运行           | 后运行                                                       | 先运行                                                       |
| 会触发新一轮Tick吗 | 会                                                           | 不会                                                         |





1. script (可以理解为外层同步代码)
   \2. setTimeout/setInterval
   \3. UI rendering/UI事件
   \4. postMessage，MessageChannel
   \5. setImmediate，I/O（Node.js） 1. Promise
   \2. MutaionObserver
   \3. Object.observe（已废弃；`Proxy` 对象替代）
   \4. process.nextTick（Node.js）  谁先运行 后运行 先运行  会触发新一轮Tick吗 会 不会



![](https://raw.githubusercontent.com/chuerFeng/pictureBed/master/img/微信图片_20210922095849.jpg)



```javascript
new Promise((resolve,reject)=>{      
  console.log("promise1")
  resolve()
}).then(()=>{                    --> then1      
  console.log("then1-1")
  new Promise((resolve,reject)=>{    
      console.log("promise2")
      resolve()
  }).then(()=>{                 --> then3
      console.log("then2-1")
  }).then(()=>{                 --> then4     
      console.log("then2-2")
  })

  console.log(22222)               
}).then(()=>{                    --> then2      
  console.log("then1-2")
})


```





第一轮

微任务队列：[then1]

首先打印p1，没啥问题，然后进入then1微任务，打印then1-1 ，然后发现个new Promise,立即执行里面的console，打印P2，

继续往下，然后发现then3微任务 ，加入到队列尾部，然后是一连串链式调用then，因为要等then3微任务执行完才能返回新promise才能后续then，所以先不处理这些链式调用的then。然后再打印222。此时then1微任务执行完成，并返回新的promise，此时主进程（执行栈）检测到then2，并将其加入到微任务队列中。



第二轮

微任务队列：[then1(已执行), then3, then2]

执行then3时又检测到微任务then4，加入队列

第三轮

微任务队列：[then1(已执行), then3(已执行), then2, then4]

【over】后面同理，依次执行完


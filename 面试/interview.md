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

![img](https://raw.githubusercontent.com//img/16557061-cb417c9b7d3883bd.png)



http请求时，会检测是否有本地缓存，如果本地命中了缓存会优先从本地取，如果没有本地缓存则浏览器再向服务器请求*(虽然本地可能有缓存，但却不一定会使用本地缓存，因为本地缓存可能是已经过期了的，为了鉴别是否过期就需要后面两种缓存规则结合)*，而http向服务器请求缓存规则分为两类： **强缓存**， **协商缓存(对比缓存)**。

#### 强缓存

直接从缓存数据库里取，无需请求服务器

![img](https://raw.githubusercontent.com//img/16557061-3b1a410230ecb1af.png)

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

### 执行上下文/作用域链/闭包

执行上下文就是当前 JavaScript 代码被解析和执行时所在环境的抽象概念， JavaScript 中运行任何的代码都是在执行上下文中运行。

执行上下文环境三种类型:   ***全局执行上下文* **  ，  ***函数执行上下文*** ， ***Eval执行上下文***



#### 执行栈

又称*调用栈*，LIFO数据结构栈(后进先出规则)

1. 当浏览器执行JavaScript时，js引擎会创建一个**全局执行上下文**并把该上下文推入调用栈。
2. 当调用一个函数first()时，js引擎会为该函数创建一个**函数执行上下文**并推入调用栈。
3. 当在first()函数内部调用另一个函数second()时，js引擎创建一个新的**函数执行上下文**并推入调用栈。
4. 当second()执行完成后调用栈将**second的上下文**弹出，控制流程到达下一个执行上下文，即**first函数执行上下文**
5. first执行完毕时，到达**全局执行上下文**
6. 所有代码执行完毕时，**全局执行上下文**被移出调用栈，调用栈空



##### **创建执行上下文**

执行上下文有两个阶段：**1) 创建阶段** 和 **2) 执行阶段**。

创建阶段有三个阶段：**1) 修改this指向** 和 **2) 创建词法环境** 和 **3) 创建变量环境**。

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



##### 执行上下文 

**执行上下文**可以看成是做事情前的**准备工作**，其中可以执行的代码有 **全局代码**，**函数代码**，**Eval代码**。

而JavaScript引擎为了管理众多的执行上下文创建了一个**执行上下文栈(ECStack)**。



###### **执行上下文栈**

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



###### 活动对象(OA对象)

在函数上下文中，通常用活动对象来表示变量对象，而函数的`变量对象`在`javascript`环境中是不可以访问的，只有当进入了`执行上下文`中该上下文的变量才会被激活，而被激活的`变量对象`就是`活动对象`，也才能被访问。





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



![image-20210921163540301](https://raw.githubusercontent.com/img/image-20210921163540301.png)



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



![](https://raw.githubusercontent.com/img/e9644cef63cdf199c4f1861afaa529f2.jpg)



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


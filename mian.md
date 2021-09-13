## Github

```js
git remote add origin http://github.com/xxx/xxx.git  关联远程仓库
git add .   /  git add xxx.file  
git commit -m "xxx"
git pull origin master
git push -u origin master
git log
git status
git branch 查看分支	
git checkout xxxx(newbranch)  切换分支
```



**github冲突解决**

+ 文件修改冲突

编辑文件，合代码，**Commit merge**提交合并

+ 文件删除引起的冲突(有人编辑了被另一个人删除的文件)





## JavaScript

### 1. 执行上下文/作用域链/闭包

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






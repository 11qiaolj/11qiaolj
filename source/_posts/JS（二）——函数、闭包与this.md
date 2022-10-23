---
title: JS（二）——函数、闭包与this
date: 2022-10-23 23:15:54
categories: 知识梳理
---
# 函数
函数在我们编程的过程中有着至关重要的作用，我们无时无刻不在使用函数！

上一节中我们了解了函数的基本概念，但对于函数还有更多的知识需要了解。
## arguments参数
>用于读取函数的参数，是一个类数组

先来试着输出下面的代码：
```
function a(a, b, c) {
    c = 10;
    console.log(arguments);
    return a + b + c;
}
a(1, 1, 1)
```
![[图片]](https://qiaolj-tuchuang.oss-cn-beijing.aliyuncs.com/img/20221023231656.png)

请注意！这里`arguments[2]`的值是 10 而不是传入的参数 1

我们把它改造一下，给最后一个参数默认值会怎么样？
```
function a(a, b, c = 3) {
    c = 10;
    console.log(arguments);
    return a + b + c;
}
a(1, 1, 1)
```
![[图片]](https://qiaolj-tuchuang.oss-cn-beijing.aliyuncs.com/img/20221023231800.png)

这里，具有默认值的 c，在 arguments 中表现为 1

>参考：MDN：Arguments 对象

## function.length
对于上面的函数 a，a 的 length 是什么意思？函数的长度？

我们来试验一下
```
console.log(a.length);
对于第一个没有默认值的函数a，会输出：3
对于第二个有默认值的函数a，会输出：2
```
由此可知，a.length 的意思是函数的**没有默认值**的参数的个数。

# 作用域及作用域链
## 作用域：
在讲解闭包之前，我们需要重申一遍作用域的概念：
1. 全局作用域：作用于全局，在全局的任何地方都能够访问到。
2. 函数作用域：在一个函数体内定义的变量只可以在函数作用域中访问到。
3. 块级作用域： 在 es6 中提出，随着新的变量声明命令 let 和 const 的出现而出现。一对{}就是一个块级作用域，由 let 和 const 定义的变量，只能在块级作用域内被访问，作用域外访问不到。

## 作用域链：
对于函数来说，当代码进入到函数当中会将函数上下文推入一个上下文栈。在函数执行完成后会将函数上下文从上下文栈弹出。将控制权返回给执行上下文。

那么在这时，上下文在执行函数时会生成一个作用域链。这个作用域链决定了各级上下文中的代码在访问变量和函数时的顺序。

例如下面这段代码：
```
var color1='blue'

function changeColor(){
    let color2='red';
    function swapColor(){
        let color3=color2
        color2=color1
        color1=color3 //这里可以使用
    }
    swapColor();
}
changeColor()

color1=color3//报错！！！
```
在这段代码中就生成了一个作用域链：
![[图片]](https://qiaolj-tuchuang.oss-cn-beijing.aliyuncs.com/img/20221023231951.png)
在这种情况下，内部上下文可以通过作用域链访问外部上下文中的一切，但外部上下文无法王文内部上下文中的任何东西。
# 闭包
在上文中我们发现，我们在外层是无法直接访问函数内部的变量的。

那么有什么办法可以改变这一情况吗？

答案是：我们可以使用闭包！

>官方解释：闭包（closure）是一个函数以及其捆绑的周边环境状态（lexical environment，词法环境）的引用的组合。

个人认为“**红宝书**”：中的解释更加清楚：闭包指的是那些引用了另一个函数作用域中变量的函数，通常是在嵌套函数中实现的。

一种常见的情况就是将函数作为返回值返回。

在函数外部调用这个返回值，可以得到函数作用域内的变量！
```
function makeFunc() {
    var name = "Mozilla";
    function displayName() {
        alert(name);
    }
    return displayName;
}
var myFunc = makeFunc();
myFunc();    //'Mozilla'
```
>当一个变量被使用时，若当前作用域没有该变量，就会向上查找，直到全局。若未找到，则报错。

这样是不是就解决了前面那一节中，外部无法访问函数内部变量的问题。

闭包还有另一个作用：让这些变量的值始终保持在内存中
```
 let nAdd
 function f1(){
　　　　let n=999;
　　　　nAdd=function(){n++}
　　　　function f2(){alert(n)}
　　　　return f2;
　　}
　　var result=f1();
　　result(); // 999
　　nAdd();
　　result(); // 1000
```
在这里，第一次result的结果是999，第二次则是1000。

我们成功的通过一个闭包保存并修改了这个result中n的值。

## 闭包的缺点：
这里我们知道，闭包可以让一个变量保存在内存中，那使用闭包的问题也随之而来了。

滥用闭包，会使很多原本应该被清理掉的变量被引用（有关浏览器的垃圾清理），这些变量清除不掉就会造成内存泄露。

## 一道经典面试题：
```
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i)      //会输出5个6
  }, i * 1000)
}
```
我们预期会输出1、2、3、4、5对吧？

可事实却是输出5个6。

怎么解决呢？
1. 闭包：
```
for (var i = 1; i <= 5; i++) {
  ;(function(j) {
    setTimeout(
        function timer() {
          console.log(j)
        }
        , j * 1000)
  })(i)
}
```
在上述代码中，我们首先使用了立即执行函数将 i 传入函数内部，这个时候值就被固定在了参数j 上面不会改变，当下次执行 timer这个闭包的时候，就可以使用外部函数的变量 j，从而达到目的。

2. setTimeout 的第三个参数：
```
for (var i = 1; i <= 5; i++) {
  setTimeout(
    function timer(j) {
      console.log(j)
    },
    i * 1000,
    i
  )
}
```
3. 把 var 换成 let 就可以了

let会生成块级作用域来保存变量。

# Js 中的 this
参考《你不知道的JavaScript（上卷）》：

1. 默认绑定：函数直接被调用，this 指向 window 
```
function f(){
console.log(this)
}
f()
```
![[图片]](https://qiaolj-tuchuang.oss-cn-beijing.aliyuncs.com/img/20221023232226.png)
2. 隐式绑定：对象的方法被调用，this 指向该对象
```
let a={
    f:function(){
        console.log(this.x)
    },
    x:1
}
a.f()
```
![[图片]](https://qiaolj-tuchuang.oss-cn-beijing.aliyuncs.com/img/20221023232250.png)
3. 显示绑定：使用call、apply、bind来显示的绑定函数的this
4. new绑定：new 的 this 指向实例上 
```
function foo(a){
    this.a=a
}
let b=new foo(2)
console.log(b.a)    //2
```
具体的关于new的内部逻辑会在后面的章节中展开介绍。
5. 箭头函数没有 this（或者说指向他所在的作用域）
简单来说，就是一个函数的 this 是在函数被调用时查找而非定义时。

## 手写 call,apply,bind
this的使用经常让人感到困惑

js 提供了一些方法来帮助我们避免这种困惑。

使用 call、apply、bind 改变 this 指向，这时this会指向我们想要的目标。

先来说说这三个函数的区别
- call：第一个参数为要指向的对象target，后面的 args1，arg2...为函数的参数。
- apply：第一个参数也是要指向的对象target，第二个参数为一个数组[...args]，数组中的每一项为函数的参数。
- bind：bind的参数与 call 相同，只不过 bind 返回了一个函数。需要再次执行（而 call 和 apply 都会直接执行）

### 手写call：
```
Function.prototype.myCall = function (obj, ...ary) {
    obj = obj || window        //obj为空则指向window
    obj.fn = this              //this指被调用的函数，将函数临时挂载到当前对象的fn属性上
    if (typeof this != "function") {
        throw new Error("这不是一个函数！")   //如果不是函数，抛出错误
    }
    const result = obj.fn(...ary)      //this指向最后一个调用函数的对象，所以指向obj
    delete obj.fn           //删除函数，防止变量污染
    return result           //返回结果
}
```
### 手写apply：
apply与call大致相同，只是第二个参数为一个数组
```
Function.prototype.myApply = function (obj, ary) {   //这里的ary是一个数组
    obj = obj || window        //obj为空则指向window
    obj.fn = this              //this指被调用的函数，将函数临时挂载到当前对象的fn属性上
    if (typeof this != "function") {
        throw new Error("这不是一个函数！")   //如果不是函数，抛出错误
    }
    var result = obj.fn(...ary)      //this指向最后一个调用函数的对象，所以指向obj
    delete obj.fn           //删除函数，防止变量污染
    return result           //返回结果
}
```
### 手写bind：
```
Function.prototype.myBind = function (obj,...args) {  
    
    const myThis = obj||window //弹出数组的第一个参数，为指向对象
    
    const self = this           //获取当前函数的this
    return function () {        //bind方法返回一个函数
        return self.apply(myThis, args)      //通过apply将这个函数指向myThis并执行
    }
}
```
看看结果：
![[图片]](https://qiaolj-tuchuang.oss-cn-beijing.aliyuncs.com/img/20221023232411.png)

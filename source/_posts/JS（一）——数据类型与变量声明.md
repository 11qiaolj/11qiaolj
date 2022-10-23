---
title: JS（一）——数据类型与变量声明
date: 2022-10-23 19:37:32
categories: 知识梳理
---
# JS
我们常说的js，实际上是EcmaScript+浏览器提供DOM和BOM

这里我们首先学习语法，也就是ES，在此之前你可能需要掌握常规的编程知识（如关键字、变量、for循环、if判断。。。等常规语法）

ES可以说是前端的重中之重，学会了他，你就能真正的开启前端的大门！
## 基本数据类型与引用数据类型
基本数据类型有 7 个：
- number 数字
- string 字符
- boolean 布尔
- undefined 未定义
- null  
- symbol 标识符
- bigInt  大整数

这里注意，基本数据类型都是开头小写的，开头大写的（如 Number，String）是他们的构造函数

引用数据类型：
- object 

只有一个？

是的，你没有看错，对js来说所有的引用数据类型都是通过objcet实现的。

那function、array、Date、Map、Set。。。这些呢？

这些在本质上都是通过object实现的。

>关于function
这里，可能有的同学会有疑问——function也属于object吗？
答案是肯定的 function是一种特殊的object，其内部实现了`[[call]]`方法用以调用。

## 类型判断
### typeof
typeof是js提供的简单的类型判断方式，但并不准确。

typeof大多数时候被用作判断基本数据类型。

typeof判断引用数据类型时都会返回`object`。

并且`typeof null`的结果同样是`object`！

*原因：在 JavaScript 最初的实现中，JavaScript 中的值是由一个表示类型的标签和实际数据值表示的。对象的类型标签是 0。由于 null 代表的是空指针（大多数平台下值为 0x00），因此，null 的类型标签是 0，typeof null 也因此返回 "object"。*

### instanceof
instanceof 用于判断引用数据类型，其原理是基于其原型链查找。

>关于手写`instanceof`会在原型链这一章介绍。

### 准确的判断类型
`Object.prototype.toString.call()`是判断类型的最佳解决方案!

该函数会返回一个`[object xxx]`的数据，`xxx` 即为判断的数据类型。
![图片](https://qiaolj-tuchuang.oss-cn-beijing.aliyuncs.com/img/object.tostring().PNG)
## 类型之间的比较
js之间的类型比较通过`==`和`===`来比较。

`===`被认为是几乎正确的，大部分时候都可以使用`===`。

>一个例外：`NaN!==NaN`

使用`==`,通常会出现一些迷惑行为。

这主要是因为类型发生了隐式转换。

简单来说：
`对象->字符串<-数字<-布尔`

在这个过程中，出现类型相同，则会比较，不同则转换类型，直到相同。

### 究其根本：
对象在转换类型的时候，会调用内置的` [[ToPrimitive]] `函数。

你也可以尝试重写 `Symbol.toPrimitive` ，该方法在转原始类型时调用优先级最高。
```
let a = {
  valueOf() {
    return 0
  },
  toString() {
    return '1'
  },
  [Symbol.toPrimitive]() {
    return 2
  }
}
1 + a // => 3
```
### 简单判断true or false
`!!`两步取反

![图片](https://qiaolj-tuchuang.oss-cn-beijing.aliyuncs.com/img/两步取反.png)
## 堆和栈
基本数据类型在内存里，存储在栈（stack）中。

而引用数据类型是在栈中存储一个引用标识，这个标识会指向堆中的一个地址。

可能这么说有点抽象，那我们来上图片吧：

例如，我们声明：
```
let A=3
let B=3
```
那这个时候，A和B都会指向数字3。因此我们也可以认为，`存在栈中的数据可以共享`。

这时我们让B=4，那B就会指向数字4。
![[图片]](https://qiaolj-tuchuang.oss-cn-beijing.aliyuncs.com/img/stack1.png)

当我们声明一个对象时，会在栈（stack）中生成一个引用，再将这个引用指向堆（heap）中的地址:
```
let A={
 a:1,
 b:'xxx'
}
let B=A
```
那么这时，他们虽然还是同时指向了引用x，然后指向heap中的一个地址。
![[图片]](https://qiaolj-tuchuang.oss-cn-beijing.aliyuncs.com/img/20221023195632.png)

如果我们此时修改B:B.a=2

那这时，B中的引用并不会改变，而是会去heap中修改指向啊的对象。
![[图片]](https://qiaolj-tuchuang.oss-cn-beijing.aliyuncs.com/img/20221023195712.png)

那这时我们再来打印A.a,我们会得到结果2

```
console.log(A.a)//2
```

这就是为什么明明我没有改变A，却得到了不合预期的值。
## 深拷贝与浅拷贝
这里，我们知道了要得到一个一模一样的对象，不能用=的方式复制。

那怎么办呢？

这时我们可以使用深拷贝或浅拷贝
### 浅拷贝
浅拷贝是指，拷贝对象的“第一层”，生成一个新的对象。

我们常用的:
- 拓展运算符`...`
- `Object.assign()`
- `Array.slice()`
等一些方法都是浅层拷贝。

但这也会面临一个问题，就是当对象里的某个值引用的依然是个对象怎么办？

例如：
```
let a={x:1}

let b={
 x:1,
 y:2,
 z:a
}
let c={...b}//浅拷贝
```
这里，`b.z`引用了对象`a`。如果只是浅拷贝的话，得到的新对象`c`中的`c.z`仍然引用的是对象`a`。

这时，就需要用到深拷贝了！

### 深拷贝：
一种简单的办法：
```
JSON.pares(JSON.stringify(obj))
```
该方法可以对对象进行深拷贝，但在遇到`Date`、`Function`或`undefined`时，会出现问题。

也可以使用函数库`lodash`中的`cloneDeep`方法。

可是我们未必想引入一个`lodash`，那这时就需要自己手动来实现。

一个一道比较经典的面试题就是：`手写实现深拷贝`:
```
function deepClone(obj = {}) {
    if (typeof obj !== 'object' || obj === null) {
        return obj  //不是引用类型，直接返回即可
    }
    if(obj instanceof Date)return new Date(obj)
    if(obj instanceof RegExp)return new RegExp(obj)
    
    let res = Array.isArray(obj) ? [] : {}   //是数组则创建空数组，是对象则传教空对象
    for (let key in obj) {
        if (obj.hasOwnProperty(key)) {      //判断是不是自身的属性
            res[key] = deepClone(obj[key])  //递归调用，拷贝深层的数据
        }
    }
    return res
}
```
## 声明变量：
在说明变量如何声明之前，我们要先来阐述作用域的概念：

### 作用域:
1. 全局作用域：作用于全局，在全局的任何地方都能够访问到。
2. 函数作用域：在一个函数体内定义的变量只可以在函数作用域中访问到。
3. 块级作用域： 在 es6 中提出，随着新的变量声明命令 let 和 const 的出现而出现。
一对`{}`就是一个块级作用域，由 let 和 const 定义的变量，只能在块级作用域内被访问，作用域外访问不到。

### var 、let 和 const
在初学js时会使用var定义变量，但在学习es6之后的内容后，就统一使用let和const来定义变量了。
#### var
一个神奇的现象就是：在var变量声明前仍然可以访问到该变量。
```
console.log(a)//undefined
var a=10
```
这是因为 var 命令存在变量提升，会在所有代码的顶部声明 `a`

上述代码实际上会转变为：
```
var a //undefined
console.log(a)
a=10
```
同时，var 声明的变量还可以重复声明，后声明的变量会覆盖先声明的变量。
```
var tmp = new Date();
function f() {
    console.log(tmp);
    if (false) {
        var tmp = 'hello world'
    }
}
f() //输出undefined
```
重复声明会覆盖前面的值，而又存在变量提升，故出现 tmp的值是`undefined `的尴尬情况。
#### let 和 const
Es6 中提出了 let 和 const 的声明方式

let 和 const不存在变量提升，同时在let 和 const声明前调用变量会存在“*暂时性死区*”

只要在声明前调用，就会报错:
![[图片]](https://qiaolj-tuchuang.oss-cn-beijing.aliyuncs.com/img/20221023200318.png)
同时let 和 const存在块级作用域，作用域外访问不到 let 声明的变量:
![[图片]](https://qiaolj-tuchuang.oss-cn-beijing.aliyuncs.com/img/20221023200340.png)
const 与 let 基本一致，不同的是 const 不能够改变值（引用类型并非如此）
![[图片]](https://qiaolj-tuchuang.oss-cn-beijing.aliyuncs.com/img/20221023200434.png)
对于引用类型，const 声明的引用类型内部的属性依旧可以改变。

如果我们想让他的属性也固定，可以调用方法Object.freeze(obj)来冻结属性

#### 其余声明方法
除了上述的三种声明方式，还有三种声明方式
- Function
- class 
- import

#### 使用变量：
当一个变量被使用时，若当前作用域没有该变量，就会向上查找，直到全局。若未找到，则报错。

### 探究变量提升
只有var有变量提升吗？
——除了 var 有变量提升，function 也存在“函数提升”。
```
f()
var f=function(){
    console.log("123")
}
//会报错 f不是一个函数
```
var 会把 f 提升上去，但f的值未定义。

然而：
```
f()
function f(){
    console.log("123")
}
//会输出123
```
函数会提升到顶部，这样就可以执行f。

那么再升级一下，var 和函数谁更优先呢？
```
var a = 1;

function a() {
    console.log(xxx)
}

console.log(a)
//会输出1
```
由此可见，函数会被升级到 var 之前来定义。

但是！
```
console.log(a)
var a = 1;

function a() {
    console.log(xxx)
}
//输出[function a]
```
我们奇怪，如果函数升级到 var 之前，那么输出不应该是 undefined 吗？
其实是，当存在 a 函数时，var a=undefined 会被忽略！



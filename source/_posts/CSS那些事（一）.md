---
title: CSS那些事（一）
date: 2022-09-23 15:46:29
categories: 知识梳理
---
# CSS
CSS在html的基础上为网页“增色”，有了CSS，我们才做出如今灵动多彩的网页。

CSS将作为面试考察的第一关，对CSS足够了解，才能成为一个合格的网友开发者。


## css 选择器及其权重
为元素写CSS样式，那么首先就要了解CSS选择器。

选择器：
- 行内样式权重为 1000                  
- ID选择器(#) —— 权重100
- 类选择器(.) —— 权重10 
- 标签/伪类/属性选择器 —— 权重1
- 通配符选择器(*) —— 权重0
- 继承没有权重。
- 遇到!important 则是最大，大于一切。

## 行内元素和块级元素
- block：是块级元素，会独占一行，能设置宽度，高度。
- inline：行内元素，设置 width 和 height 无效，由内容撑开，
- inline-block：能设置宽度高度，且不会独占一行。

## 盒子模型
盒子模型由：内容 content，内边距 paddig，边框 border，外边距 margin 组成。
![[图片]](https://qiaolj-tuchuang.oss-cn-beijing.aliyuncs.com/img/盒模型.jpeg)

通常我们设置标准盒子模型（W3C 盒子模型）的宽度时，要考虑他的宽度是：
`width=content（设定宽度）+ padding + border `

设定的宽度与实际的宽度不符。这让我们在使用时需要计算。

css3 提供了一种新方法：
`box-sizing:border-box；`

可以让我们盒子的宽度就是我们所设定的 width，不会因内边距和边框而撑开。

我们也称这种盒模型叫 IE 盒子模型或怪异盒子模型。

## margin 常见问题
### 1. 外边距合并和传递
当我们在设置两个上下排列的 p 标签的上下外边距时，会发生外边距合并（重叠）。
```
<style>
.p{
    margin-bottom:10px;
    margin-top:15px;
}
</style>

<body>
    <p>段落1</p>
    <p></p>
    <p></p>
    <p></p>
    <p>段落2</p>
</body>
```
当这样两个 p 标签上下排列时，他们的间距并不是：`15+10=25px`

会发生外边距合并，他们中间将只有 `15px`（较大者）的距离。

同时，他们中的空标签，会被忽略，不会影响两个有内容的标签。
### 2. 负值的 margin
我们在操作中有时会给 margin 加一个负值，目的是让盒子朝我们期望的方向移动。

当为盒子添加 `margin-top/left：负数` 的时候，这个盒子会朝上或左移动。

但我们给盒子添加 `margin-bottom/right：负数` 时，这个盒子之后的元素会向着这个盒子的位置移动。（自身宽高缩小）

## float 
float可以说是CSS的必备招式

float会为元素设置浮动，浮动的元素自动变为行内块元素，且会“一个挨一个”的排列在一行上。

这种排练元素的手法在flex出现之前非常常见，但同时也会伴随诸多问题。

其中最常问的就是`清除浮动`,那就涉及到BFC的应用
## BFC
_嵌套元素的外边距塌陷_：

我们在为子元素加一个向上的 margin 时，期待的效果是子元素可以与父元素隔开，而事实却不会这样。

这个margin会加到父元素上，父元素依此移动，但父子元素还是紧贴在一起。

为了解决这样的现象，我们有几种办法： 
1. 加一个透明边框。 
2. 使用父元素 padding 的方式来隔开父子。 
3. 父元素 overflow:hidden。

第三种解决方案就是利用了BFC

BFC 是块级格式化上下文。可以为内容添加独立的渲染区域。

BFC 的常见应用有： 
1. 清除浮动。 
2. 独立渲染。 
3. 解决外边距塌陷。

形成 BFC 的几种条件：
- 浮动元素———float 不是 none 
- 定位元素———position 是 absolute 或 fixed 
- 块级元素———overflow 值不为 visible、clip 的块元素
- 行内快元素、flex 元素

### 清除浮动
这是一道常见的手写题：
```
.clearfix :after{
    content:'';
    height:0;
    overflow:hidden;
    clear:both;
    display:block;
}
.clearfix{
    *zoom:1;        //浏览器兼容性
}
```
## 水平居中及垂直居中方式
简单来说，分几种常用的手法：
1. 最最基础的方式是的使用`margin:0 auto`来使元素水平居中。
2. 使父元素中的子元素垂直居中的方式是：父元素`line-height`与自身高度相同。
3. 接下来就是使用`定位+位移`的方法:
```
1.定位+margin
position:absolute;
left:50%;
top:50%;
margin:-1/2height 0 0 -1/2width;
```
```
2.定位+tarnslate
position:absolute;
left:50%;
top:50%;
transform:translate(-50%,-50%)
```
4. 用flex来实现居中：
```
display:flex;
justify-content:center;
align-items:center;
```
flex布局在工作中的使用频率极高也极其方便，需要重点学习！
## flex 弹性布局

flex 弹性布局提供了一种不同于传统 float 的布局方式，通过主轴和侧轴实现元素的排列。
- 我们先来看父元素的属性(只列举一些常见的）：
```
display:flex;   //定义父元素为flex盒子
flex-direction:row/column;     //定义flex盒子中的主轴方向
flex-warp:warp/nowarp;         //定义flex盒子的主轴元素是否自动换行
justify-content:center/space-around/space-between;  //定义元素在主轴上的排列方式
align-items:center;     //定义元素在副轴上的排列方式
```
- 对于子元素来说有一个必须知道的知识点：`flex:1`代表什么？

简单来说，`flex:1`是一个语法糖，浏览器会解析为对应的属性。
```
flex-grow:1;    //会填充主轴的剩余空间
flex-shrink:1;  //会在主轴空间元素不足时，被压缩
flex-basis:0%;  //元素在扩展或收缩时，基于的自身原本宽度
```
那我们就知道了，这三个属性配合使用，就可以使所有的子元素都保持同样的大小。

在开发中通常需要擅用这三个属性，来控制flex中子元素的大小。

通过这些特性，实现了flex“弹性”盒子，让布局更加方便。

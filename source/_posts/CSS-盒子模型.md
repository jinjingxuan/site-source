---
title: 盒子模型
date: 2020-11-26 11:27:54
categories: CSS
---

* 盒子模型
* 脱离文档流
* BFC
* 清除浮动

## 盒子模型

* w3c标准盒模型
  * 包括 width, height, margin, padding, border
  * 可视宽度是 width + padding + border
  * box-sizing: content-box;
* ie盒模型
  * 也包括 width, height, margin, padding, border
  * 可视宽度 width
  * box-sizing: border-box;

## 脱离文档流

* 所谓的**文档流**，指的是元素排版布局过程中，元素会自动从左往右，从上往下的流式排列。
* **脱离文档流**，也就是将元素从普通的布局排版中拿走，其他盒子在定位的时候，会当做脱离文档流的元素不存在而进行定位。
* **浮动 ( float ) 和绝对定位 ( position:absolute )**
  * 均脱离文档流
  * 均不占位
  * 浮动情况下，其他元素会自动在其右边排列。绝对定位会完全忽视其存在。

## BFC

* BFC的定义:

```js
BFC（Block formatting context ）“块级格式上下文”。 是用于布局块级盒子的一块渲染区域。并且与这个区域的外部毫无关系。
```

- BFC的布局规则

```js
 内部的Box会在垂直方向，一个接一个地放置。

 Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠。

 每个盒子（块盒与行盒）的margin box的左边，与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。

 BFC的区域不会与float box重叠。

 BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。

 计算BFC的高度时，浮动元素也参与计算。
```

* 触发BFC的条件:

```js
满足下列条件之一就可以触发BFC

 1：根元素，即html元素

 2：float的值不为none

 3：overflow的值不为visible

 4：display的值为inline-block、table-cell、table-caption

 5：position的值为absolute或者fixed
```

- BFC的作用

（1）**可以阻止元素被浮动的元素覆盖**（可做两栏布局自适应）

[![1](https://img-blog.csdnimg.cn/20190428161126130.png)](https://img-blog.csdnimg.cn/20190428161126130.png)

 触发红色盒子的BFC后

[![2](https://img-blog.csdnimg.cn/20190428161306268.png)](https://img-blog.csdnimg.cn/20190428161306268.png)

（2）**解决高度塌陷**：我们知道当浮动的盒子的父元素没有高度时，会出现高度塌陷现象。

[![3](https://img-blog.csdnimg.cn/20190428162141491.png)](https://img-blog.csdnimg.cn/20190428162141491.png)

 父盒子触发BFC可以解决这个问题,根据布局规则的最后一条。

（3）**解决同一个BFC区域的垂直方向margin塌陷的问题**

[![4](https://img-blog.csdnimg.cn/20190428165048481.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxMjU3MTI5,size_16,color_FFFFFF,t_70)](https://img-blog.csdnimg.cn/20190428165048481.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxMjU3MTI5,size_16,color_FFFFFF,t_70)

 分为两个不同的BFC之后可以解决

## 清除浮动

* 给父盒子添加高度
* 在浮动元素后使用一个空元素如`<div class="clear"></div>`，并在CSS中赋予`.clear{clear:both;}`属性即可清理浮动。
* 父盒子设置`overflew`不为`visible`，触发父盒子的`BFC`，浮动元素也参与高度计算
* :after伪元素，给浮动元素的容器添加一个clearfix的class，然后给这个class添加一个:after伪元素实现元素末尾添加一个看不见的块元素清理浮动。

```css
.clearfix:after{
  content: "020"; /*content设置成一个看不见的空格*/
  display: block; 
  height: 0; 
  clear: both; 
  visibility: hidden;  
}
```

通过上面的例子，我们不难发现清除浮动的方法可以分成两类：

1. 利用 clear 属性，包括在浮动元素末尾添加一个带有 clear: both 属性的空 div 来闭合元素，其实利用 :after 伪元素的方法也是在元素末尾添加一个内容为一个点并带有 clear: both 属性的元素实现的。

2. 触发浮动元素父元素的 BFC (Block Formatting Contexts, 块级格式化上下文)，使到该父元素可以包含浮动元素，关于这一点。
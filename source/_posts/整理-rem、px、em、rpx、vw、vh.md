---
title: rem、px、em、rpx、vw、vh
date: 2018-09-17 13:51:54
categories: 整理
---

### 1.px

在Web页面初期制作中，我们都是使用“px”来设置我们的文本，因为他比较稳定和精确。但是这种方法存在一个问题，当用户在浏览器中浏览我们制作的Web页面时，他改变了浏览器的字体大小，这时会使用我们的Web页面布局被打破。 于是就有了em

<!--more-->

### 2.em

em是相对长度单位。它的单位长度是根据元素的文本垂直长度来决定的。可以作用在width、height、line-height、margin、padding、border等样式的设置上。 如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸。 默认1em=16px。如果在body选择器中声明font-size=62.5%,则1em=10px。

```html
<style>
	.parent{ 
			font-size:5em; /*80px*/
			height:10em;/*800px*/
	}
    .child{
			font-size:2em;/*160px*/
			height:2em;/*320px*/
	}
</style>
<body>
	<div class="parent">
    <footer class="child">
        <div class="wrapper">
        </div>
    </footer>
</div>
```

在不设置元素font-size的情况下，em总是根据父元素的font-size来确定长度；即使元素设置了font-size，多次嵌套使用em也往往会造成疏忽，不仅使用前需要大量计算，而且不能保证没有漏网之鱼。这将是一个繁杂而低效率的工作。 于是有了rem.

### 3.rem

rem不是依据父元素——而是依据根元素（root element）来确定其长度。 

我们一般给根元素设置一个容易计算的font-size

```html
<style>
    html {
        font-size: 62.5%;   /* 10px */
    }
    div {
        font-size: 2.4rem;  /* 24px */
        width: 64rem;   /* 640px */
        border: 0.1rem solid #ccc;  /* 1px */
    }
</style>
<body>
    <div class="div1">
        <div class="div2">
        </div>
    </div>
</body>
```

### 4.rpx

| 设备         | rpx换算px (屏幕宽度/750) | px换算rpx (750/屏幕宽度) |
| :----------- | :----------------------- | :----------------------- |
| iPhone5      | 1rpx = 0.42px            | 1px = 2.34rpx            |
| iPhone6      | 1rpx = 0.5px             | 1px = 2rpx               |
| iPhone6 Plus | 1rpx = 0.552px           | 1px = 1.81rpx            |

* rpx（responsive pixel）: 可以根据屏幕宽度进行自适应。规定屏幕宽为750rpx。如在 iPhone6 上，屏幕宽度为375px，共有750个物理像素，则750rpx = 375px = 750物理像素，1rpx = 0.5px = 1物理像素。 

* 建议小程序的设计稿以750 x 1334 的物理分辨率进行设计

* pt: html css中的使用的单位像素px: 实际上指的是逻辑像素pt

* px: photoshop测量中的但是实际上指的是物理像素, 物理像素即表示的是一个点, 大小固定

* 一个pt可以包含多个物理像素px，在iphone6中一个单位的逻辑像素包含2个物理像素iphone的分辨率为375\*667 实际上指的是逻辑像素为375\*667, 所以一般移动端的设计图纸一般是给的是750*1334,  是因为一个逻辑像素pt包含两个物理像素px


### 5.vw,vh

* vw和vh是css3中的新单位，是一种视窗单位，在小程序中也同样适用。
* 小程序中，窗口宽度固定为100vw，将窗口宽度平均分成100份，1份是1vw
* 小程序中，窗口高度固定为100vh ，将窗口高度平均分成100份，1份是1vh
* 所以，我们在小程序中也可以使用vw、vh作为尺寸单位使用在布局中进行布局，但是一般情况下，百分比+rpx就已经足够使用了,所以它们的出场机会很少。
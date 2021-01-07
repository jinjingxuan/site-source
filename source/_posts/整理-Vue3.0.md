---
title: Vue3.0
date: 2020-12-17 11:27:54
categories: Vue
---

## Vue.js 3.0

* 源码组织方式的变化
  * 采用 TS 重写
  * 使用 Monorepo 管理项目结构
* Composition API
* 性能提升
* Vite

## 目录结构

> packages目录下有许多模块/包。Monorepo 是管理项目代码的一个方式，指在一个项目仓库 (repo) 中管理多个模块/包 (package)，不同于常见的每个模块建一个 repo。[Vue3.0 中的 monorepo 管理模式](https://juejin.cn/post/6844903961896435720)

* compiler-core：平台无关的编译器
* compiler-dom：浏览器平台下的编译器，依赖于compiler-core
* compiler-sfc：（single file component）编译单文件组件
* compiler-ssr：服务端渲染的编译器
* reactivity：数据响应式系统
* runtime-core：平台无关的运行时
* runtime-dom：浏览器平台下的运行时
* runtime-test：为了测试的运行时，dom树是js对象，可运行于所有js环境里
* server-renderer：服务端渲染
* shared：vue内部使用的一些公共的API
* size-check：一个私有的包，不会发布到npm，在treeshaking后检查包的大小
* template-explorer：实时编译组件。输出render函数
* vue：构建完整版的vue

## 构建版本

* cjs（commonJs规范）
  * vue.cjs.js
  * vue.cjs.prod.js
* global（可以直接通过script引入，增加全局vue对象，runtime是只包含运行时）
  * vue.global.js
  * vue.global.prod.js
  * vue.runtime.global.js
  * vue.runtime.global.prod.js
* browser（通过script type=module方式引入）
  * vue.esm-browser.js
  * vue.esm-browser.prod.js
  * vue.runtime.esm-browser.js
  * vue.runtime.esm-browser.prod.js
* bundler（没有打包的代码，要配合打包工具）
  * vue.esm-bundler.js
  * vue.runtime.esm-bundler.js

## Composition API

在 vue2.x 中采用的是 Options API

> `在vue2中如何组织代码的`，**我们会在一个vue文件中methods，computed，watch，data中等等定义属性和方法，共同处理页面逻辑，**我们称这种方式为Options API

* 包含一个描述组件选项(data, methods, props等)的对象
* Options API 开发复杂组件，同一个功能逻辑的代码被拆分到不同选项

### Composition API

* vue.js 3.0 新增的一组 API
* 一组基于函数的 API
* 可以更灵活的组织组件的逻辑

[Composition API图示](https://user-images.githubusercontent.com/499550/62783026-810e6180-ba89-11e9-8774-e7771c8095d6.png)

## 性能提升

* 响应式系统升级：采用 Proxy

* 编译优化

  * Vue.js 2.x中通过标记静态根节点，优化 diff 的过程，静态节点仍需要 diff 
  * Vue.js 3.0中标记和提升所有的静态根节点，diff 的时候只需要对比动态节点内容
    * Fragments（升级 vetur 插件，没有根节点也不会报错，会创建一个 Fragment 片段）
    * 静态提升（再次编译可以跳过静态根节点）
    * Patch flag
    * 缓存事件处理函数
  * 例子：[模板编译网址](vue-next-template-explorer.netlify.app)

     ```html
     <div id='app'>
         <div>static root
     		<div>static node</div>
         </div>
         <div>static node</div>
         <div>static node</div>
         <div :id="id">{{ count }}</div>
         <button @click="handler"></button>
     </div>
     <!-- 1. 删掉根节点试一下
          2. 右上角options中选择 hoistStatic 提升静态节点
          3. 右侧可以找到 patch flag
          4. 右上角options可以开启缓存 -->
     ```

* 源码体积的优化

  * 移除一些不常用的 API：inline-template, filter
  * Tree-shaking

## Vite

回顾浏览器加载模块（type=module会自动添加上defer属性）过程: **加载模块并执行是在DOM树创建完毕之后，DOMContentLoaded之前执行**

```html
<div id="app">hello world</div>
<script>
	window.addEventListener('DOMContentLoaded', () => {
        console.log('DOMContentLoaded')
  })
</script>
<script type="module" src="./index.js"></script>
```

```js
// index.js
import { forEach } from './utils.js'

const app = document.querySelector('#app')
console.log(app.innerHTML)

const arr = [1, 2, 3]
forEach(arr, item => {
    console.log(item)
})
```

```js
// 输出
// Hello World
// 1 2 3
// DOMContentLoaded
```

### Vite vs Vue-CLI

* Vite 在开发模式下不需要打包可以直接运行（开发模式下使用script type=module，不需要打包代码）
* Vue-CLI 开发模式下必须对项目打包才可以运行
* 生产环境下使用 Rollup 打包（基于ES Modules的方式打包）

### Vite 特点

* 快速冷启动
* 按需编译
* 模块热更新

### Vite创建项目

```shell
npm init vite-app <project-name>
cd <project-name>
npm install
npm run dev
```

### 基于模板创建项目

```shell
npm init vite-app --template react
```


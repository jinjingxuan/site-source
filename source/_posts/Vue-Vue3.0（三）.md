---
title: Vue3.0（三）
date: 2021-1-13 11:27:54
categories: Vue
---

# Vite 概念

* Vite 是一个面向现代浏览器的一个更轻，更快的 web 应用开发工具
* 它基于 ECMAScript 标准原生模块系统（ES Modules）实现
* 主要为了解决开发阶段使用 webpack-dev-server 仍启动过慢，HMR 热更新反应慢

# Vite 项目依赖

* Vite：命令行工具
* @vue/compiler-sfc：编译 .vue 单文件组件，在 vue2 中是 vue=template-compiler

# 基础使用

* vite serve：开启 web 服务器，不需要编译所有文件，启动速度十分快
* vite build
  * Rollup
  * Dynamic import
    * Polyfill

## vite serve

> 只有具体使用模块的时候才会编译，HMR也立即编译当前所修改的文件

![sNcPaV.png](https://s3.ax1x.com/2021/01/13/sNcPaV.png)

## vue-cli serve

> 不管模块是否被执行，都会被打包
>
> HMR：会自动以这个文件为入口重新 build 一次，所有涉及到的依赖也都会被加载一遍

![sN6hgH.png](https://s3.ax1x.com/2021/01/13/sN6hgH.png)

## 打包 or 不打包

使用 webpack 打包的两个原因

* 浏览器环境并不支持模块化（已渐渐不存在 ）
* 零散的模块文件会产生大量的 http 请求

# 开箱即用

* TypeScript - 内置支持
* less / sass / stylus / postcss - 内置支持（需要单独安装）
* JSX
* Web Assembly

# Vite 特性

* 快速冷启动
* 模块热更新
* 按需编译
* 开箱即用
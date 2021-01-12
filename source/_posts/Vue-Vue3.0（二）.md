---
title: Vue3.0（二）
date: 2020-12-17 11:27:54
categories: Vue
---

# Vue.js 3.0响应式原理

* 使用 Proxy 对象实现属性监听
* 多层属性嵌套，只有在访问属性过程中处理下一级属性
* 默认监听动态添加的属性
* 默认监听属性的删除操作
* 默认监听数组索引和 length 属性
* 可以作为单独的模块使用

# 核心方法

* reactive/ref/toRefs/computed
* effect
* track
* trigger

## reactive

* 接收一个参数，判断参数是否是对象
* 创建拦截器对象 handler，设置 get / set /deleteProperty
* 返回 Proxy 对象
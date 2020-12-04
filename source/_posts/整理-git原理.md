---
title: git原理
date: 2020-07-09 10:00:54
categories: git
---

* git常见命令：<https://blog.csdn.net/web_csdn_share/article/details/79243308> 
* git reset原理：<https://www.cnblogs.com/wangwenjin2026/p/11549285.html> 
* commit规范
* 什么是fork：https://www.liaoxuefeng.com/wiki/896043488029600/900937935629664
* git rebase

## commit 规范

- feat: 新功能(feature)
- fix: 修复 bug
- docs: 文档(documents)
- style: 代码格式(不影响代码运行的格式变动，注意不是指 CSS 的修改)
- refactor: 重构(既不是新增功能，也不是修改 bug 的代码变动)
- test: 提交测试代码(单元测试，集成测试等)
- chore: 构建或辅助工具的变动
- misc: 一些未归类或不知道将它归类到什么方面的提交

## fork

参考：https://www.liaoxuefeng.com/wiki/896043488029600/900937935629664

## git rebase

* git rebase -i：合并多次提交记录
* git rebase master：与 merge 的区别是不会有 merge 的 commit，保持干净的提交记录

参考[这一次彻底搞懂 Git Rebase](https://www.codercto.com/a/45325.html)，[完美生活：git rebase -i | Linux 中国](https://zhuanlan.zhihu.com/p/141871803)，[【Git】rebase 用法小结](https://www.jianshu.com/p/4a8f4af4e803)
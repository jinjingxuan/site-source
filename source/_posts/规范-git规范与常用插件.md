---
title: git规范与常用插件
date: 2020-12-18 11:27:54
categories: 规范
---

* git
  * commit 规范
  * sourceTree
  * 合并多条 commit 
* VSCode 插件
* vue-dev-tools
* Jsonview
* Iterm2 + oh my zsh

## git

* git常见命令：<https://blog.csdn.net/web_csdn_share/article/details/79243308> 
* git reset原理：<https://www.cnblogs.com/wangwenjin2026/p/11549285.html> 
* 什么是fork：https://www.liaoxuefeng.com/wiki/896043488029600/900937935629664

### commit 规范

> `commitizen`是一个nodejs命令行工具，通过交互的方式，生成符合规范的git commit，使用如下

```js
git add .
git cz
```

#### 如何安装

```js
// 全局安装
npm install -g commitizen 
// 或本地安装
$ npm install --save-dev commitizen
// 安装适配器
npm install cz-conventional-changelog
```

### sourceTree

* 便于查看分支图表
* Dark 主题

### 合并多条 commit 

#### git rebase

参考[这一次彻底搞懂 Git Rebase](https://www.codercto.com/a/45325.html)，[完美生活：git rebase -i | Linux 中国](https://zhuanlan.zhihu.com/p/141871803)，[【Git】rebase 用法小结](https://www.jianshu.com/p/4a8f4af4e803)

```js
git rebase -i head~3 // 合并最近三条commit
```

弹出编辑界面

> pick b931dac 修改test2为test
> pick efd10a0 feat: 群引流加好友时间
> pick 860aea3 合并
>
> \# commands
>
> pick：保留该commit（缩写:p）
>
> reword：保留该commit，但我需要修改该commit的注释（缩写:r）
>
> edit：保留该commit, 但我要停下来修改该提交(不仅仅修改注释)（缩写:e）
>
> squash：将该commit和前一个commit合并（缩写:s）
>
> fixup：将该commit和前一个commit合并，但我不要保留该提交的注释信息（缩写:f）
>
> exec：执行shell命令（缩写:x）
>
> drop：我要丢弃该commit（缩写:d）

我一般选择`rff`命令，使用最上面的提交并修改，后两条合并，然后继续进入编辑界面修改提交记录，:wq 退出

### git reset --soft [commitID]

> 带 `--soft` 参数的区别在于把改动内容添加到暂存区 相当于执行了`git add .`

## VSCode 常用插件

* Git History： 查看 git 历史
* Leetcode：刷题必备
* Bracket Pair Colorizer：括号匹配
* GitLens：在 vscode 上使用 git 功能
* Chinese (Simplified) Language Pack for Visual Studio Code：中文设置

> 安装后，在 `locale.json` 中添加 `"locale": "zh-cn"`，即可载入中文（简体）语言包。要修改 `locale.json`，你可以同时按下 `Ctrl+Shift+P` 打开**命令面板**，之后输入 "config" 筛选可用命令列表，最后选择**配置语言**命令。

* Babel JavaScript：JavaScript 语法高亮显示
* ESLint
* Live Server
* open in browser
* Minapp：微信小程序标签、属性的智能补全
* wechat-snippet：微信小程序代码辅助
* wxml：微信小程序 wxml 格式化以及高亮组件
* Vetur: 支持vue文件的语法高亮显示，除了支持template模板以外，还支持大多数主流的前端开发脚本和插件，比如Sass和TypeScript
* vscode-icons
* vue-helper插件：代码提示，函数跳转
* Codelf：右键变量命名
* any-rule：正则大全

## vue-dev-tools

> 控制台调试 vue

## Jsonview

* [地址](https://github.com/gildas-lormeau/JSONView-for-Chrome)

> Chrome 查看 json 数据

## Iterm2 + oh my zsh

[iTerm2 + Oh My Zsh 打造舒适终端体验](https://segmentfault.com/a/1190000014992947)






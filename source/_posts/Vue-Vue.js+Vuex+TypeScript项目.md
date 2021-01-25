---
title: Vue.js + Vuex + TypeScript项目
date: 2020-1-14 11:27:54
categories: Vue
---

# 创建项⽬

安装 Vue CLI： 

```shell
npm i -g @vue/cli
```

```shell
vue create edu-boss-fed 

（可以同过上下键，空格来选择）

Vue CLI v4.5.6 
? Please pick a preset: Manually select features (手动选择配置)

? Check the features needed for your project: Babel, TS, Router, Vuex, CSS Pre-processors, Linter 

? Use class-style component syntax? Yes 

? Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? Yes (TS用于编译TS语法，Babel用于编译es6)

? Use history mode for router? (Requires proper server setup for index fallback in production) No (使用hash模式)

? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): Sass/SCSS (with dart-sass) （css预处理器）

? Pick a linter / formatter config: Standard （standard风格）

? Pick additional lint features: Lint on save, Lint and fix on commit（保存和commit时都检查）

? Where do you prefer placing config for Babel, ESLint, etc.? In dedicated config files 
（单独配置文件）

? Save this as a preset for future projects? No （是否保存以上配置）

⚓ Running completion hooks... 
� Generating README.md... 

� Successfully created project topline-m-89. 
� Get started with the following commands: 

$ cd edu-boss-fed 
$ npm run serve
```


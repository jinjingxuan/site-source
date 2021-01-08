---
title: webpack（四）
date: 2020-09-08 14:47:00
categories: webpack
---

* Webpack sideEffects
* Code Splitting
* mini-css-extract-plugin
* 文件名 Hash

## Webpack sideEffects

> 副作用：模块执行时除了导出成员之外所做的事情
>
> 比如一个 css 文件没有导出，可认为有副作用

```js
|--components
		--button.js
		--heading.js
		--link.js
		--index.js
|--index.js
```

```js
// components/index.js

export { default as Button } from './button'
export { default as Link } from './link'
export { default as Heading } from './heading'

// 入口文件 index.js
import { Button } from './components'
// 只想载入 Button 却载入了所有模块
```

```js
// webpack.config.js

module.exports = {
  mode: 'none',
  entry: './src/index.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          'css-loader'
        ]
      }
    ]
  },
  optimization: {
    sideEffects: true, // 标识为true就会去package.json中去检查有无副作用
  }
}

// package.json
{
  "name": "31-side-effects",
  "version": "0.1.0",
  "main": "index.js",
  "author": "zce <w@zce.me> (https://zce.me)",
  "license": "MIT",
  "scripts": {
    "build": "webpack"
  },
  "devDependencies": {
    "css-loader": "^3.2.0",
    "style-loader": "^1.0.0",
    "webpack": "^4.41.2",
    "webpack-cli": "^3.3.9"
  },
  // "sideEffects": false  //设置为false说明没有副作用，则多余的模块不会被打包
  "sideEffects": [
    "./src/extend.js", // 标识一下有副作用而不想被删除的模块，防止不会被打包
    "*.css"
  ]
}

```

## 代码分割

> 所有模块都打包到一起，但是并不是每个模块在启动时都是必要的
>
> 分包，按需加载

* 多入口打包：一个页面一个入口，公共部分提取
* 动态导入

### 多入口打包配置

```js
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  mode: 'none',
  entry: {
    index: './src/index.js',  // 多入口
    album: './src/album.js'
  },
  output: {
    filename: '[name].bundle.js' // 生成对应的文件名
  },
  optimization: {
    splitChunks: {
      // 自动提取所有公共模块到单独 bundle 一个文件
      chunks: 'all'
    }
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          'css-loader'
        ]
      }
    ]
  },
  plugins: [
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      title: 'Multi Entry',
      template: './src/index.html',
      filename: 'index.html',
      chunks: ['index']  // index.html 只会引入打包后的 index.js
    }),
    new HtmlWebpackPlugin({
      title: 'Multi Entry',
      template: './src/album.html',
      filename: 'album.html',
      chunks: ['album'] // album.html 只会引入打包后的 album.js
    })
  ]
}
```

### 动态导入

> 需要某个模块时再去加载，动态导入的模块会被自动分包

```js
// import posts from './posts/posts'
// import album from './album/album'
// 删除固定导入

const render = () => {
  const hash = window.location.hash || '#posts'

  const mainElement = document.querySelector('.main')

  mainElement.innerHTML = ''
  // 根据 hash 值动态导入
  if (hash === '#posts') {
    // 魔法注释添加文件名
    import(/* webpackChunkName: 'components' */'./posts/posts').then(({ default: posts }) => {	
      mainElement.appendChild(posts())
    })
  } else if (hash === '#album') {
    // 魔法注释添加文件名
    import(/* webpackChunkName: 'components' */'./album/album').then(({ default: album }) => {
      mainElement.appendChild(album())
    })
  }
}

render()

window.addEventListener('hashchange', render)
```

## mini-css-extract-plugin

> mini-css-extract-plugin将css单独提取出来，如果 css 超过 150KB 时需要单独提取
>
> 提取出来后还需要用OptimizeCssAssetsWebpackPlugin进行压缩
>
> 因为 webpack 内置的压缩插件只打包 js 文件

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')
const TerserWebpackPlugin = require('terser-webpack-plugin')

module.exports = {
  mode: 'none',
  entry: {
    main: './src/index.js'
  },
  optimization: {
    minimizer: [
      new TerserWebpackPlugin(),
      new OptimizeCssAssetsWebpackPlugin() 
    ]
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          // 'style-loader', // 将样式通过 style 标签注入
          MiniCssExtractPlugin.loader, // 直接通过 link 方式引入
          'css-loader'
        ]
      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin()
  ]
}
```

> 为什么ptimizeCssAssetsWebpackPlugin插件放在这不放在 plugins 数组中呢 ?
>
> 因为放在 plugins 说明在任何时候都会加载这个插件，而我们放到 minimizer 中，只会在生产环境开启 minimizer 时工作
>
> 但是这时会发现 js 没有压缩，因为我们设置了minimizer，webpack会认为我们自定义了压缩插件，所以我们还要添加回去压缩 js 的插件TerserWebpackPlugin。

## 文件名 Hash

```js
  plugins: [
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      title: 'Dynamic import',
      template: './src/index.html',
      filename: 'index.html'
    }),
    new MiniCssExtractPlugin({
      filename: '[name]-[contenthash:8].bundle.css' // 文件级别的hash
      // filename: '[name]-[chunkhash].bundle.css'  // chunk级别
      // filename: '[name]-[hash].bundle.css'       // 项目级别，一个文件改变，所有hash全部改变
    })
  ]
}
```


---
title: san-store
date: 2021-07-13 11:27:54
categories: San
---

## san-store

san-store是san框架用于状态管理的工具，和vuex类似，但是还是有区别的，首先复习一下`vue`

> vue中更改`state`的方式有且只有一个： `mutation`，用`commit` 来触发，限制是只能同步修改。
>
> 想要异步需要`dispatch` 一个 `action`，`action`中再触发`commit`修改`state`，`action`中可以做异步操作。

```js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
// 通过 dispathch 触发
store.dispatch('increment')
```

![vuex](https://vuex.vuejs.org/vuex.png)

**San-store**中则不会有`mutation`和`action`之分，只存在`action`，通过`dispatch`来触发。

![san-store](https://raw.githubusercontent.com/baidu/san-store/master/doc/flow.png)

### store

**store**的概念和vuex中一样，那么一个初始化的store是什么样的呢？（san-update库实现了一个更新对象的函数，同时随更新过程输出新旧对象的差异结构）

```js
import {Store} from 'san-store';
import {builder} from 'san-update';

let myStore = new Store({
    initData: {
        user: {
            name: 'your name'
        }
    },

    actions: {
        changeUserName(name) {
            return builder().set('user.name', name);
        }
    }
});
```

### getState

san-store中用getState来获取state状态的值

```js
myStore.getState('user.name');
```

### Action

Action是一个函数，可以直接定义在store中，也可以通过 addAction 方法可以为 Store 添加 Action。

两个参数：

* payload载荷
* context对象：getState, dispatch

返回值：

* 返回一个 Promise 时，当前 Action 为异步
* 返回一个 builder 或什么都不返回时，当前 Action 为同步

[使用builder构建更新函数](https://github.com/baidu/san-update#%E4%BD%BF%E7%94%A8builder%E6%9E%84%E5%BB%BA%E6%9B%B4%E6%96%B0%E5%87%BD%E6%95%B0)

```js
function actionFun(payload, {getState, dispatch}) {
	// payload 载荷数据
  // getState 获取state状态
  // dispatch 触发其他action
}

// 同步 Action
store.addAction('setUserName', function (name) {
    return builder().set('user.name', name);
});

// 异步 Action
store.addAction('login', function (payload, {getState, dispatch}) {
    if (getState('user.name') === payload.name) {
        return;
    }

    dispatch('logining', true);
    return userService.validate(payload).then(() => {
        dispatch('setUserName', payload.name);
        dispatch('logining', false);
    })
});
```

### dispath

**参数**

- `{string} name` action名称
- `{*} payload` 给予的数据

**返回值**

- action 为同步时，返回 undefined
- action 为异步时，返回 Promise

```js
store.dispatch('login', {
    name: 'errorrik',
    password: 'xxxxx'
});
```

### connect

connect 用于将 store 实例与 san 组件连接，从而：

- 当 store 数据变更时，连接的组件数据也进行相应的变更
- 组件内部像调用方法一样 dispatch action，组件实现时无需关心对具体 store 的依赖

两个参数：

* mapStates
* mapActions

connect.san 返回一个执行 connect 操作的函数，这个函数可以接受一个组件类作为参数

```js
export default class UserComponent extends Component {
  // 插值表达式直接访问 mapStates
  static template = `
		<template>
			<div>{{ name }}</div>
		</template>
	`,
  
  changeName() {
    // 通过 this.actions 访问 mapActions
  	this.actions.change()
  }
}

connect.san(
    {name: 'user.name'},
    {change: 'changeUserName'}
)(UserComponent);
```


---
title: Vue Router
date: 2020-06-29 10:57:05
top:
categories:
- 学习笔记
tags:
- web前端
- vue
---

断断续续看了好多遍  `Vue Router` 的[官方文档](https://router.vuejs.org/zh/)，终于下定决心来写一篇笔记了.....

![](/images/vr-1.jpg)

OK，开始正题

<!--more-->

完整介绍的话内容太多，本文主要介绍工作中较常用到的几个知识点

### router/route

#### router

`Vue Router` 对象，包含了 `Vue Router` 的一些属性和方法

**Vue Router 属性**

- router.app: 配置了 `router` 的Vue根实例。
- router.mode: 路由使用的[模式](https://router.vuejs.org/zh/api/#mode)。
- router.currentRoute: 当前激活的路由实例，同 `this.$route`。

**Vue Router 方法**

- router.beforeEach: 全局前置守卫，必须调用 `next()` 方法。
- router.beforeResolve: 全局解析守卫，必须调用 `next()` 方法。
- router.afterEach: 全局后置钩子，无 `next()` 方法。
- router.push: 打开一个新路由/页面，**会**在浏览器添加历史记录。
- router.replace: 打开/替换为一个新路由/页面，**不会**在浏览器添加历史记录。
- router.go: 前进 `n` 个页面，`n` 可为负数。
- router.back: 等同于 `router.go(-1)`。
- router.forward: 等同于 `router.go(1)`。
- router.getMatchedComponents: 返回目标位置或是当前路由匹配的组件数组(是组件的定义/构造类，不是实例)，一般用于**服务端渲染**的数据预加载使用。
- router.resolve: 解析目标位置。
- router.addRoutes: 动态添加更多的路由规则。
- router.onReady: 把一个回调排队。
- router.onError: 注册一个回调，该回调在路由导航过程中出错时被调用。

#### route

当前激活的路由实例，所有属性**只读**

- $route.path: 当前路由的**绝对**路径。
- $route.params: 当前路由上 `key/value对象` 参数。
- $route.query: 当前对象的 `URL` 上的 `key/value对象` 参数，会显示在浏览器地址栏上。
- $route.hash: 当前路由的 `hash` 值(带 `#`)。
- $route.fullPath: 完成解析后的 `URL`，包含查询参数和 `hash` 的完整路径。
- $route.matched: 一个匹配路由的数组。从根路由开始到当前所在路由的所有嵌套路径片段组成的数组。
- $route.name: 当前路由的 `name`。
- $route.redirectedForm: 重定向来源的路由的名字。

#### 组件注入

通过在 Vue 根实例的 `router` 配置传入 router 实例，下面这些属性成员会被注入到每个子组件。

- **this.$router** : `router` 实例。
- **this.$route** : 当前激活的路由实例，所有属性**只读**。

### 导航守卫

#### 全局导航守卫

- beforeEach: 全局前置守卫，导航前，一定要调用 `next()` 来 `resolve` 这个钩子。
- beforeResolve: 全局解析守卫，导航前，和 `beforeEach` 类似，区别是导航在确认前，**同时在所有组件内守卫和异步路由组件解析之后**，解析守卫才会被调用。
- afterEach: 全局后置钩子，导航后，无 `next()` 方法也不会改变导航本身(可添加页面定位等功能)。

#### 路由独享的守卫

- beforeEnter: 路由独享守卫，配置在路由上的守卫，**该路由及其子路由**都会共享该守卫。

#### 组件内守卫

- beforeRouteEnter: 渲染该组件对应的路由被 `confirm` 前调用，**不能**获取 `this`，但可通过传回调(唯一一个可以传回调给 `next` 的守卫)给 `next()` 来访问组件实例。

  ```js
  beforeRouteEnter (to, from, next) => {
    next(vm => {
      // 通过 `vm` 访问组件实例
    })
  }
  ```

- beforeRouteUpdate: 路由改变，但是该组件被复用时调用。

- beforeRouteLeave: 导航离开该组件的对应路由时调用。

- ```js
  const Foo = {
    template: `...`,
    beforeRouteEnter (to, from, next) {
      // 在渲染该组件的对应路由被 confirm 前调用
      // 不！能！获取组件实例 `this`
      // 因为当守卫执行前，组件实例还没被创建
    },
    beforeRouteUpdate (to, from, next) {
      // 在当前路由改变，但是该组件被复用时调用
      // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
      // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
      // 可以访问组件实例 `this`
    },
    beforeRouteLeave (to, from, next) {
      // 导航离开该组件的对应路由时调用
      // 可以访问组件实例 `this`
    }
  }
  ```

#### 完整的导航解析流程

1. 导航被触发。
2. 在失活的组件里调用 `beforeRouteLeave` 守卫。
3. 调用全局的 `beforeEach` 守卫。
4. 在重用的组件里调用 `beforeRouteUpdate` 守卫（2.2+）。
5. 在路由配置里调用 `beforeEnter` 。
6. 解析异步路由组件。
7. 在被激活的组件里调用 `beforeRouteEnter` 。
8. 调用全局的 `beforeResolve` 守卫（2.5+）。
9. 导航被确认。
10. 调用全局的 `afterEach` 钩子。
11. 触发 `DOM` 更新。
12. 用创建好的实例调用 `beforeRouteEnter` 守卫传递给 `next` 的回调函数。

### 其他

**falsy/falsey(虚值)**

- 在 `Boolean` 上下文中认定为 `false` 的值。
- `Javascript` 在需要用到 `Boolean` 类型的值的上下文中使用强制类型转换将值转换为 `Boolean` 值，比如条件语句和循环中。
- 目前 `Js` 中只有7个 `falsy` 值：
  - `false`、`0`、`0n`、`""/''/`、\`\`、 `null`、`undefined`、`NaN`

**滚动行为**

- `scrollBehavior(to, from, savedPosition)`: 添加在路由实例 `new Router({})` 中，用于自定义切换时页面如何滚动。
- 该功能只在支持 `history.pushState` 的浏览器中可用。
- `keep-alive` 中无效，因为 `keep-alive` 已自动保留了之前的状态。
- 返回滚动的位置信息：
  - `{ x: number, y: number }` : 具体位置。
  - `{ selector: string, offset? : { x: number, y: number }}` : 锚点+偏移量
- [更多参考](https://router.vuejs.org/zh/guide/advanced/scroll-behavior.html)
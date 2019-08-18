---
title: omp项目总结
date: 2019-08-18 16:15:00
top:
categories:
- 日记本
tags:
- web前端
- vue
- iview
- git
- webpack
- echarts
---

连续加班1个月了，天天加班10点多，到家11点半，周末也在加班，基本没有哪天是12点以前睡的。明显感觉扛不住了，身体越来越差，一度处于 **猝死的边缘** .....

好在这个项目终于完成了(其实还没完全结束，还在修bug阶段，然后又被安排去另一个项目了...7天开发时间，1个星期+2个周六，**感觉要猝死了** .....)

吐槽不要太多

当然，收获也是很多

所以，就总结了一哈....

<!--more-->

![](/images/omp-1.jpg)

## 项目

**开发周期**

周期没啥好写的，就是1个月，从开发到上线，总共一个月...

**吐槽**

吐槽不要太多...

开发周期太短，项目太赶了。

最初计划是1个月时间上线。开发2周，测试联调2周，然而实际情况是，开发和测试同步进行。

开发1个页面，测试就开始测，然后就是前期测试一直在催，页面开发好了没，开发好了没。。。 这 xx$%^^&*， 开发怎么能赶上测试的进度....

其次，开发预计时间，安排的极不合理。所有开发预计时间都是最理想状态的。就是没人打扰，不会遇到难点，只做开发一件事的时间。然而实际情况是，有太多的第三方因素会影响开发进度，比如：

- 遇到难点：自己或者其他同事遇到难点，解决一个难点就花了2个人一上午的时间
- 封装公共组件：这些任务是没有考虑在开发预计时间内的，那个时间只考虑了页面的开发时间...
- 其他事情：
  - 比如开会，各种会议。
  - 测试叫去改bug等(测试和开发同步进行的....)
  - 后端接口有问题，联调的时候一直在等后端的接口正常

总之就是，**时间太赶，2周时间开发根本不够** 。

然后吧，就是疯狂加班了。。。

住的离公司近还好，像我这种这么远的，每天10点多下班，回去到家就11点半了，每天要12点多才能睡觉，而且，**长期没有休息**，周末能休息下还好，问题是周末还得加班.....

再然后吧，身体越来越差了，天天都困的要死，感觉要猝死了。。。

所以，接下来，要**赶紧改变下当前的状态**，这是当前最重要的事了..

## 收获

ok，吐槽完毕，说下收获吧。

花费了这么多精力，以及无数个加班。。。收获还是挺多的。。

### 开发规范

#### 敏捷开发流程

项目采用的是敏捷开发，和传统开发最大的区别，就是没有预期的开发时间。

传统开发会有充足的开发计划，xxxx时间段开发，xxxx时间段测试阶段，然后上线。目的是 按时交付**高质量的产品**。

而敏捷开发(omp项目就算吧)，是有需求了，立刻开发，并且 **开发和测试同步进行**，目的 **最短时间内** 交付产品。

#### git使用规范：git-flow

项目使用 `git-flow` 规范来管理代码，常见的分支结构有：

- **master** ：用来存储发布后的稳定代码分支，一般会使用 `tag` 标签进行管理
- **hotfix-xxx** ：热修复线上bug的分支，从 `master` 创建，直接合并到 `master`
- **dev** ：开发分支，从 `master` 创建
- **test** ：测试分支，从 `master` 创建
- **release** ：发布分支，从 `master` 创建
- **feature-xxx** ：新需求分支，从 `master` 创建，合并到 `dev`
- ![](/images/omp-2.jpg)

其中有几点要求：

- 一个功能一个分支，一起提交到远程服务器，待稳定后，删除对应的 `feature` 分支
- 流程基本上是：`master` --> `feat-xx` --> `dev` --> `test` --> `release` --> `master`
- 不能从 `dev` ` merge` 代码到 `feature` 分支，如果有依赖其他分支的功能从其他分支直接 `merge` 到当前分支：`feat-A` --> `feat-B`

### 代码方面

#### vue

**生命周期及钩子函数**

`vue` 的生命周期包含下面8个阶段：

- **beforeCreate** ：`vue` 实例的挂载元素 `$el` 和数据对象 `data` 都是 `undefined`，还未初始化
- **created** ：完成了 `data` 的初始化，`el` 还未初始化
- **beforeMount** ：`vue` 实例的 `$el` 和 `data` 都初始化了，相关的 `render` 函数首次被调用。实例已完成以下的配置：编译模板，把 `data` 里面的数据和模板生成 `html` 。注意此时 `html` 还没有挂载到页面上
- **mounted** ：在 `el` 被新创建的 `vm.$el` 替换，并挂载到实例上去之后调用。实例已完成以下的配置：用上面编译好的 `html` 内容替换 `el` 属性指向的 `DOM对象` 。完成模板中的 `html` 渲染到 `html` 页面中，此过程中进行 `ajax交互` 
- **beforeUpdate** ：在数据更新之前调用，发生在虚拟 `DOM` 重新渲染和打补丁之前调用。可以在该钩子中进一步地更改状态，不会触发附加的渲染状态
- **updated** ：在由于数据更改导致的虚拟 `DOM` 重新渲染和打补丁之后调用。调用时，组件 `DOM` 已经更新，所以可以依赖与 `DOM` 的操作。然而在大多数情况下应该避免在此期间更改状态，因为这可能会导致无限循环。该钩子函数在服务器端渲染期间不可用
- **beforeDestory** ：在实例销毁之前调用。实例仍然完全可用
- **destoryed** ：在实例销毁之后调用。调用后，所有的事件监听器会被移除，所有的子实例也会被销毁。该钩子函数在服务器端渲染期间不被调用

**父子组件的传值**

- **父 --> 子** ：
  - 用 `props` 传值
  - 可以设定 `类型` 和 `默认值`
  - `props` 传的值在 `data` 里不能重复定义
  - `props` 传的值默认会双向绑定，及 子页面的值改变了，会导致父页面值的改变，如果期望单向绑定，有2种方法解决这个问题：
    - **子页面data里定义 aa: props的值**
    - **子页面使用 `computed` 的值**
- **子 --> 父** ：
  - 用 `this.$emit('aa', params)` 传事件
  - 在父组件使用 `@aa = 'yy'` 来捕获事件

**组件的封装**

太多了，后续补充...

**双向绑定的深刻理解（important！！）**

**对象的双向绑定**

[官网说明](https://cn.vuejs.org/v2/guide/reactivity.html#检测变化的注意事项)

`vue` 中的双向绑定，只有在页面加载时，在 `data` 里定义了的对象，才能自动双向绑定，如果 `data` 里没有定义这个对象，直接修改 `v-model` 的值，对应组件的值(UI)是不会自动修改的。

```js
var vm = new Vue({
  data: {
    a: 1
  }
})

// `vm.a` 是响应式的

vm.b = 2
// `vm.b` 是非响应式的
```



如果要双向绑定未在 `data` 里定义的对象的属性，应该使用下面的方法

- **添加单个属性** ：

  ```js
  // `Vue.set(object, propertyName, value)`
  Vue.set(vm.someObject, 'b', 2)
  ```

  ```js
  // `this.$set(object, propertyName, value)`，`this.$set` 是 `Vue.set` 的别名
  this.$set(this.someObject, 'b', 2)
  ```

- **添加多个属性** ：

  直接使用 `Object.assign()` 或 `_.extend()` 添加到对象上的新属性不会触发更新。应该使用 原对象与要混合进去的对象的属性**一起创建一个新的对象**

  ```js
  // 代替 `Object.assign(this.someObject, {a: 1, b: 2})`
  this.someObject = Object.assign({}, this.someObject, {a: 1, b: 2})
  ```

**数组的双向绑定**

[官网说明](https://cn.vuejs.org/v2/guide/list.html#数组更新检测)

**数组：能双向绑定的情况**

- **变异方法(修改自身数组)** ：

  - push()
  - pop()
  - shift()
  - unshift()
  - splice()
  - sort()
  - reverse()

- **非变异方法/替换数组(返回新数组)** ：

  - filter()
  - concat()
  - slice()

  使用 `非变异方法` 时，要使用一个新数组替换原数组

  ```js
  example1.items = example1.items.filter(function(item) {
    return item.message.match(/Foo/)
  })
  ```

**数组：不能双向绑定的情况**

由于 `JavaScript` 的限制， `Vue` **不能** 检测以下数组的变动：

- 利用索引直接设置一个数组项的值，例如：`vm.items[indexOfItem] = newValue`
- 修改数组的长度，例如：`vm.items.length = newLength`

举个例子

```js
var vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
vm.items[1] = 'x'	// 不是响应性的
vm.items.length = 2	// 不是响应性的
```

为了解决第一个问题，可以使用以下两种方式：

```js
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
// vm.$set
vm.$set(vm.items, indexOfItem, newValue)
```

```js
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
```

为了解决第二个问题，可以使用 `splice`

```js
vm.items.splice(newLength)
```

#### iview

**Form表单**

心得两点：

**validator  的使用**

![](/images/omp-3.jpg)

![](/images/omp-4.jpg)

![](/images/omp-5.jpg)

![](/images/omp-6.jpg)

**reset 的使用**

![](/images/omp-7.jpg)

**table**

基本就是一些配置吧

参考 [iview table](https://www.iviewui.com/components/table)

**其他**

参考 [iview 官网](https://www.iviewui.com/docs/guide/install)

#### echarts

`echarts` 要显示图表需要两个步骤

- **初始化**
- **使用 setOption** 方法更新数据

所以

对于 **静态图表** 来说，上面2个步骤可以放到一起

对于 **动态图表** 来说，上面2个步骤则要分开，并且每次获取数据后(获取数据方式不限)需要使用 `setOption` 方法更新数据

举个例子

- **初始化图表**

  ![](/images/omp-8.jpg)

- **初始化 websocket**

  ![](/images/omp-9.jpg)

- **websocket获取对象并调用 setOption 方法更新图表**

  ![](/images/omp-10.jpg)

  ![](/images/omp-11.jpg)

#### vue-router

**跳转传值**

- 使用 `query` ：该模式下 `to` 的地址只能使用 `path` 而不能使用 `name`

- 使用 `params` ：无要求

  ```html
  <!-- 命名的路由 -->
  <router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>
  
  <!-- 带查询参数，下面的结果为 /register?plan=private -->
  <router-link :to="{ path: 'register', query: { plan: 'private' }}">Register</router-link>
  ```

  ```js
  let params = {
    type: 'add', 
    code: 'aaa'
  }
  this.$router.push({name: 'terminalEdit', params: params)
  ```

**跳转监听事件**

- 使用 `watch` 监听

  ```js
  watch: {
    '$route' (to, from) {
      // 跳转到当前页面和离开当前页面都会触发该监听事件
      // 跳转到当前页面时，to.params 是获取的参数
      // 离开当前页面时，from.params 是当前页的参数
      let params = to.params
      if (params && params.vehicleId) {
        this.vehicleId = params.vehicleId
        this.init()
      }
    }
  }
  ```

- 在 `mounted` 的时候也可以添加捕获

  ```js
  mounted () {
    let params = this.$route.params
    if (params && params.vehicleId) {
      this.vehicleId = params.vehicleId
      this.init()
    }
  }
  ```

#### webpack

后续补充....

### 工具方面

#### SourceTree

**分支的使用**

互相切换

合并

没啥好说的....

**pull注意项**

如果你本地有修改了但未 `commit`  的代码，这时候 `git pull` 的话，会提示 `pull` 下来的代码未提交(自动合并)，这时候，你 **一定要提交 pull 下来的代码！！**

**贮藏**

巧用 **贮藏**

把当前未提交的代码贮藏起来，然后去做其他的任务，之后回来，随时启用贮藏的代码

![](/images/omp-14.jpg)

**重置到之前的提交**

如果 `commit` 没有 `push` 到远程服务器，可以 `重置到当前提交`

如果 `commit` 已经在 远程服务器上了，**暂时还不知道怎么重置** .....

#### WebStorm

- **eslint 配置** ：

  ![](/images/omp-12.jpg)

- **highLighting Level** ：这个不能全局修改，有个替代的方法，把 `warning` 提示关掉，但并没有从根本上解决问题

  ![](/images/omp-13.jpg)

## 感受及总结

### 技能方面

说下接下来技能方面的计划吧

感觉官方文档还是很重要的，比如上面说的，`vue` 中双向绑定无效，`iview` 中的 `validator` 和 `reset` 无效等问题，官网其实都有很好的描述。

所以，再仔细过一遍官方文档，还是很有必要的。

so，接下来几个要做的：

- **vue-loader 官方文档**
- **vuex 官方文档**
- **TypeScript** ：放后一步

### 接下来的重心

这个月基本就是在疯狂加班了，没时间做饭，没时间健身，没时间做任何事...

所以，接下来有几点要改变的：

- 减少加班
- 学习新技能
- 重新开始健身 (今年前5个月打卡了90天，6月到现在2个半月了吧，打卡不到10天....)

### 题外话

公司的椅子坐的好舒服。。。

本来想买个公司同款的椅子(单买1k多一点)，然后卖家只对公，不卖给个人。。。

然后 *一怒之下* ，买个了3k的人体工程学椅子。。。

emmmm...

少去两次医院就回本了

少去三次就赚了

四次就血赚！！...

然后，终于可以愉快的在家里加班了！...

(๑•̀ㅂ•́)و✧
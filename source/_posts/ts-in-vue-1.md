---
title: typescript vue 实战(1)
date: 2020-09-02 14:57:46
top:
categories:
- 学习笔记
tags:
- typescript
- vue
- web前端
---

`typescript` 对 `vue 2.x` 的支持不是很好，但我们还是有必要学习一下。

本文会列出 `vue` 中常用功能在 `typescript` 里的写法。

### 基于类的组件

单页面是以类的形式使用的，所以用 `typescript` 定义 `vue` 应使用下面的语法

- 在 `script`  里添加 `lang='ts'`
- 在 `Component`里定义组件的 `name`

<!--more-->

```html
<script lang="ts">
  import { Component, Vue } from 'vue-property-decorator'
  @Component({
    name: 'HelloTs'
  })
  export default class HelloTs extends Vue {}
</script>
```

`javascript` 的代码代码如下：

```html
<script>
  export default {
    name: 'HelloTs'
  }
</script>
```

#### 引入其他组件

- 使用 `import` 引入其他组件
- 在 `Component.components` 里添加引入的组件
- 在 `html` 模板里使用组件

```html
<template>
  <div class="main">
    <Login></Login>
  </div>
</template>

<script lang="ts">
  import { Component, Vue } from 'vue-property-decorator'
  import Login from '@/components/Login.vue'
  @Component({
    name: 'HelloTs',
    components: {
      Login
    }
  })
  export default class HelloTs extends Vue {}
</script>
```

### 基础属性

#### data

- 直接定义即可。

```typescript
@Component
export default class HelloTs extends Vue {
  public name: string = 'Jane'
	public age: number = 18
}
```

#### prop

- 使用 `import` 引入 `Prop`
- 在 `@Prop` 装饰器中添加其他属性

```typescript
import { Component, Prop, Vue } from 'vue-property-decorator'
@Component
export default class HelloTs extends Vue {
  @Prop()
  public name!: string
  @Prop({ required: true, type: Number })
	public age!: Number
  @Prop({ required: true, type: String, default: (x: string) => `China: ${x}`})
	public address!: string
}
```

#### Computed

- 直接使用 `get` 返回
- 使用 `set` + `get` 定义可修改的 `computed`

```typescript
export default class HelloTs extends Vue {
  public name: string = 'Jane'
  public age: number = 18
  // Computed
  get sayHi (): string {
    return `hi ${this.name}`
  }
  get showAge (): number {
    return this.age + 2
  }
  set showAge (newAge: number) {
    this.age = newAge
  }
}
```

#### Methods

- 直接定义

```typescript
import { Component, Vue } from 'vue-property-decorator'
@Component
export default class HelloTs extends Vue {
  public name: string = 'Jane'
  public age: number = 18
  public sayHi (name: string): void {
    console.log(`hi ${this.name}`)
  }
}
```

#### Watchers

- `Watch` 的用法和 `Javascript` 中的 `handler` 语法类似
- 使用 `import` 导入 `Watch`
- 在 `Watch` 装饰器中添加 `监听对象` 和其他属性
- 添加 `handler` 方法

```typescript
import { Component, Watch, Vue } from 'vue-property-decorator'
@Component
export default class HelloTs extends Vue {
  public name: string = 'Jane'
  public age: number = 18
  public likes: object = {
    sport: 'sing',
    color: 'blue'
  }
  
  @Watch('likes', {
    immediate: true,
    deep: true
  })
  public likesChanged (): void {
    console.table(this.likes)
  }
}
```

#### Emit

- 父组件接受 `Emit`  的参数为子组件 `return的值` + `Emit函数自身的参数`
- 使用 `import` 导入 `Emit`
- 在 `Emit` 装饰器中添加 `this.$emit('xxx')` 的方法，如未添加，使用下面定义的函数A(用`-`分隔)
- 执行完函数 `A` 后会自动调用 `this.$emit('A',params)` 方法
- 在父组件调用 `Emit`

```typescript
// ./HelloTs.vue: 子组件
import { Component, Emit, Vue } from 'vue-property-decorator'
@Component
export default class HelloTs extends Vue {
  public name: string = 'Jane'
  public age: number = 18

  @Emit('addToCount')
  public addToCount (x: number, y: number, z:number): number {
    console.table({ x, y, z })
    return x + y + z
  }
}
```

```html
// ./Father.vue: 父组件
<template>
  <div class="main">
    <HelloTs @addToCount="addToCount"></HelloTs>
  </div>
</template>

<script lang="ts">
  import { Component, Vue } from 'vue-property-decorator'
  import HelloTs from './HelloTs.vue'
  @Component({
    components: {
      HelloTs
    }
  })
  export default class Father extends Vue {
    public addToCount (total: number, x: number, y: number, z:number): void {
      console.log({ total, x, y, z })
    }
  }
</script>
```

### 生命周期钩子

- 直接使用

```typescript
import { Component, Vue } from 'vue-property-decorator'
@Component
export default class HelloTs extends Vue {
  public name: string = 'Jane'
  public age: number = 18

  public mounted () {
    console.log('HelloTs mounted ~')
  }
}
```

### Mixins

- `Mixins` 文件 `extends Vue` 
- 引入文件 `extends Mixins` 

#### 编写Mixins

- 新建 `mixin` 文件夹
- 新建 `ProjectMixin.ts` 文件，
- 添加文件内容，和 `.vue` 中的 `<script>` 标签内一样

```typescript
// @/mixin/ProjectMixin.ts
import { Component, Vue } from 'vue-property-decorator'

@Component
class ProjectMixin extends Vue {
  public proName: string = 'My project'
  public setProjectName (newValue: string): void {
    this.proName = newValue
    console.log('修改成功')
  }
  public showProjectName (): void {
    console.log('proName: ', this.proName)
  }
}
export default ProjectMixin
```

#### 引入Mixins

- 使用 `import` 引入 `Mixins`
- 引入 `Mixins` 文件 `ProjectMixin.ts`
- 修改继承 `extends Vue` 为 `extends Mixins(ProjectMixin)`
- 调用 `ProjectMixin.ts` 中的变量和方法

```typescript
// @/components/HelloTs.vue
import { Component, Mixins } from 'vue-property-decorator'
import ProjectMixin from '@/mixin/ProjectMixin'

@Component
export default class Father extends Mixins(ProjectMixin) {
  public mounted () {
    this.showProjectName()
    this.setProjectName('New Project Name')
    this.showProjectName()
  }
}
```

### Vuex

- 在 `typescript` 中使用 `Vuex` 需要引入2个包 `vuex-module-decorators` 和 `vuex-class`

#### 编写Vuex

- 在 `store` 文件夹下新建 `modules` 文件夹
- 在 `modules` 文件夹下新建 `user.ts` 文件
- `user.ts` 文件添加内容
- `store.index.ts` 内引入 `user`

```typescript
// @/store/modules/user.ts
import { VuexModule, Module, Mutation, Action } from 'vuex-module-decorators'

@Module({ namespaced: true })
class User extends VuexModule {
  public vuexName: string = ''
  public vuexValue: string = ''

  get vuexValueUpper (): string {
    return this.vuexName.toUpperCase()
  }

  @Mutation
  public setName (newName: string): void {
    this.vuexName = newName
    this.vuexValue = 'vuexValue: ' + this.vuexName
  }

  @Action
  public updateName (newName: string): void {
    this.context.commit('setName', newName)
  }
}
export default User
```

```typescript
// @/store/index.ts
import Vue from 'vue'
import Vuex from 'vuex'
import User from './modules/user'

Vue.use(Vuex)

export default new Vuex.Store({
  modules: {
    User
  }
})

```

#### 使用Vuex

- 引入 `vuex-class` 中的 `namespace`
- 定义 `modules` 常量
- 定义及调用 `Vuex`

```typescript
// @/components/HelloTs.vue
import { Component, Vue } from 'vue-property-decorator'
import { namespace } from 'vuex-class'

const user = namespace('User')
@Component
export default class HelloTs extends Vue {
  @user.State
  public vuexName!: string
  @user.Getter('vuexValueUpper')
  public vUpper!: string
  @user.Action
  public updateName!: (name: string) => void
}
```


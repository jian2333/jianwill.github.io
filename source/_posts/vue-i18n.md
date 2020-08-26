---
title: vue-i18n：vue下的一种多语言方案
date: 2020-08-26 07:43:14
top:
categories:
- 学习笔记
tags:
- web前端
- vue
- 多语言
---

[`vue-i18n`](https://kazupon.github.io/vue-i18n/zh/) 是 `vue` 下的一个插件，用于让项目支持国际化多语言。

### 安装

```bash
npm install vue-i18n --save
```



### 引入vue-i18n

1. 将 `vue-i18n` 注入到`vue`中。注入后在任何 `vue组件` (.vue结尾的文件)内，都可以使用 `this.$t('xxxx')` 来调用。

2. 新建 `VueI18n` 实例。
   1. 新建默认语言。
   2. 新建待翻译语言(json文件)。
   3. 新建 `VueI18n` 实例时导入上面文件。
3. 新建 `Vue` 实例时引入 `VueI18n` 实例。

<!--more-->

```js
// ./i18n/index.js
import Vue from 'vue'
import VueI18n form 'vue-i18n'
import 'en' from './i18n/locales/en.json'
import 'zh' from './i18n/locales/zh.json'

Vue.use(VueI18n)

const DEFAULT_LANG = 'zh'
const messages = {
  en,
  zh
}

const i18n = new VueI18n({
  locale: DEFAULT_LANG,
  messages: messages
})

export default i18n

----------------------------
// main.js
import Vue from 'vue'
import i18n from './i18n'

new Vue({
  i18n
}).$mount('#app')
```



### 使用

[官方文档](https://kazupon.github.io/vue-i18n/zh/)有详情的介绍，本文只介绍常用的几种使用方法。

#### json编写

- 使用 `{}` 设置变量

- 使用 `@xx:yy:zz` 引用 `json` 中**已存在的变量**

  ```json
  {
    en: {
      hello: 'hello { name }, nice to meet you ~',
    },
    en1: {
      alsoHello: '@:en.hello' // 和en.hello一样，也要传入变量 { name:xx }
    }
  }
  ```

  

#### 使用场景

- `vue`实例中：

  ```html
  <span>{{$t('en.hello')}}</span>
  ```

  ```js
  // vue中的 <script>内
  hello () {
    let hello = this.$t('en.hello')
    console.log(hello)
  }
  ```

- `非vue` 实例中(比如`js文件`)：

  ```js
  // 注意没有$符号
  // ./demo.js
  import i18n from './i18n'
  
  hello () {
    let hello = i18n.t('en.hello')
    console.log(hello)
  }
  ```

#### 使用方式

##### 常规翻译

- 直接使用即可。

  ```js
  // en.json
  {
    en: {
      hello: 'hello world'
    }
  }
  
  // hello.vue
  let hello = this.$t('hello')
  ```

##### 带变量的翻译

- 使用 `{}` 设置变量。

- 使用 `Object `、`Array` 等传入变量。

  ```js
  // ---------- 对象
  // en.json
  {
    en: {
      hello: 'hello { name }, nice to meet you ~'
    }
  }
  
  // hello.vue
  let hello = this.$t('en.hello', { name: 'Jack' })
  
  // ---------- 数组
  // en.json
  {
    en: {
      hello: 'hello { 0 }, nice to meet { 1 } ~'
    }
  }
  
  // hello.vue
  let hello = this.$t('en.hello', ['Jack', 'you'])
  ```

### 优化

#### 调用方式

我们发现，在 `vue` 组件中是通过 `this.$t('xxx')` 来调用，在 `非vue` 组件中是通过引入 `i18n` ，然后 `i18n.t('xxx')` 来调用。

那每次调用的时候，我们得区分**是否是 `vue` 组件**， 然后**重复、多次引入  `i18n` **。

这明显增加了代码的复杂性。有个直接的解决方法就是，把 `i18n` 挂载到全局的 `windows` 下，之后在任何地方都可以通过 `i18n.t()` 来调用了。

```js
// ./i18n/index.js
import Vue from 'vue'
import VueI18n form 'vue-i18n'
import 'en' from './i18n/locales/en.json'
import 'zh' from './i18n/locales/zh.json'

Vue.use(VueI18n)

const DEFAULT_LANG = 'zh'
const messages = {
  en,
  zh
}

const i18n = new VueI18n({
  locale: DEFAULT_LANG,
  messages: messages
})

window.i18n = i18n // +++++
export default i18n

----------------------------
// main.js
import Vue from 'vue'
import i18n from './i18n'
// 其他 import 文件 +++++

new Vue({
  i18n
}).$mount('#app')
```

#### 添加修改语言方法

我们在 `i18n` 里添加一个修改语言的方法。

- 该函数做了以下3件事情。
- 将当前语言保存到 `localStorage` 中；
- 给 `body` 添加语种相关的 `class `，优化样式时使用。
- 将当前语种存到 `Vue` 的全局配置中，以便未来使用。

```js
// ./i18n/index.js
import Vue from 'vue'
import VueI18n form 'vue-i18n'
import 'en' from './i18n/locales/en.json'
import 'zh' from './i18n/locales/zh.json'

Vue.use(VueI18n)

const DEFAULT_LANG = 'zh'
const messages = {
  en,
  zh
}

const i18n = new VueI18n({
  locale: DEFAULT_LANG,
  messages: messages
})

// 修改语言的函数
export const setup = lang => {
  if (!lang) {
    lang = window.localStorage.getItem(LOCALE_KEY)
    if (!messages[lang]) {
      lang = DEFAULT_LANG
    }
  }
  window.localStorage.setItem(LOCALE_KEY, lang)

  Object.keys(messages).forEach(lang => {
    document.body.classList.remove(`lang-${lang}`)
  })
  document.body.classList.add(`lang-${lang}`)
  document.body.setAttribute('lang', lang)

  Vue.config.lang = lang
  i18n.locale = lang
}

window.i18n = i18n 
export default i18n
```

#### 热加载(待验证)

  ```js
  // main.js
  if (module.hot) {
    module.hot.accept(['./i18n/locales/en', './i18n/locales/zh'], function () {
      i18n.setLocaleMessage('en', require('./i18n/locales/en').default)
      i18n.setLocaleMessage('zh', require('./i18n/locales/zh').default)
      // 同样可以通过 $i18n 属性进行热更新
      // app.$i18n.setLocaleMessage('en', require('./i18n/locales/en').default)
      // app.$i18n.setLocaleMessage('ja', require('../i18n/locales/zh').default)
    })
  }
  ```
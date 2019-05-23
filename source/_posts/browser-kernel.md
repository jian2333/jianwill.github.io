---
title: 浅谈浏览器内核
date: 2019-05-23 18:23:37
top:
categories:
- 学习笔记
tags:
- web前端
- 浏览器
---

### 内核简介

一个完整的浏览器包含浏览器内核和浏览器外壳(shell)，浏览器核心––––内核分为两部分：**渲染引擎** 和 **js引擎** 。由于js引擎越来越独立，所以现在说的浏览器内核一般指 **渲染引擎** 。

**渲染引擎** ：解析DOM文档和CSS规则并将内容和样式显示到浏览器中。也叫 **排版引擎** ，我们常说的浏览器内核主要指的就是 **渲染引擎** 。

**js引擎** ：解析和执行JS脚本的模块，如 V8引擎，JavaScriptCore 等。

<!--more-->

### 5 大主流浏览器(按时间顺序)

#### IE 浏览器

IE 是微软其下的浏览器，IE4~IE11 使用的都是 `Trident` 内核。

Windows10 发布后，IE 将其内置浏览器命名为 **Edge**，使用新内核 `EdgeHTML` 。

IE 从版本11开始，初步支持 `WebGL` 技术。IE8 的js引擎是 `jscript` ，IE9开始使用 `Chakra` ，这两个版本差别很大，`Chakra` 无论是速度还是标准化方面都很出色。

Edge的JS引擎是 `Chakra`。

#### Opera 浏览器

Opera 是挪威 Opera Software ASA 公司其下的浏览器。

1995年，Opera 公司发布第一版 Opera 浏览器，并使用 `Presto` 内核。

2016年奇虎360和昆仑万维收购了 Opera 浏览器，并改用当时 Google 开源的 `webkit` 内核。

之后 Opera 又跟随 Google 将浏览器内核改为 `Blink` 。

#### Safari 浏览器

Safari 是 Apple 公司自研的浏览器，主要用于 Apple 自身的产品。

第二次浏览器大战也是从苹果公司发布 Safari 开始的。

2003年，苹果公司在苹果手机上开发 Safari 浏览器，利用自己得天独厚的手机市场份额使 Safari 浏览器迅速成为世界主流浏览器。

Safari 是最早使用 `webkit` 内核的浏览器，后来升级为 `webkit2` 内核，也是现在苹果默认的浏览器。

Safari 的JS引擎是 `JavaScriptCore` 。

#### Firefox 浏览器

Firefox 是 Mozilla 公司旗下的浏览器，也就是 网景 公司后来的浏览器。

网景被收购后，网景人员创办了 Mozilla 基金会，这是一个非营利性的组织，他们在2004年推出自己的浏览器 Firefox。

Firefox 采用 `Gecko` 内核。

Firefox 的JS引擎是 `SpiderMonkey` 。

`Gecko` 是一个开源的项目，代码完全公开，因此受到很多人的青睐。

#### Chrome 浏览器

Chrome 是 Google 公司的浏览器。

Chrome 最开始使用 `webkit` 分支出的 `Chromium` 内核，后来升级为 `Blink` 内核。

Chrome 的JS引擎是 `V8` 。

#### 浏览器与JS引擎

![](/images/bk-2.jpg)

### 5 大内核

#### Trident 内核

也就是俗称的 `IE内核` 。

`Trident` 内核迭代列表

![](/images/bk-1.jpg)

使用该内核的浏览器有：IE浏览器，多核浏览器。

#### Presto(Opera前内核)(已废弃)

Opera12.17及更早版本曾使用此内核，现已停止开发并废弃。

Opera在2013年2月宣布放弃 `Prosto`，转而跟随 Chrome 使用 `webkit` 的分支 `Chromium` 引擎作为其核心。

在 Chrome 与 2013年推出 `Blink` 核心后，Opera 也紧跟随其脚步使用 `Blink` 核心。

#### Webkit 内核

苹果公司研发的内核，后来升级为 `webkit2`。

`webkit`内核包括：`WebCore渲染引擎` 和 `JavaScriptCore解析引擎`，均是从 KDE 的 `KHTML` 和 `KJS` 衍生而来的。

2008年，Google 发布了 Chrome 浏览器，浏览器使用的内核叫做 `Chromium` 。

`Chromium` fork 自开源引擎 `Webkit` ，Google 还研发了自己的JS引擎，即 `V8引擎` ，极大的提高了 JavaScript 的运行速度。

使用该内核的浏览器有：Safari，Android 默认浏览器，多核浏览器。

#### Gecko 内核

Netscape6 开始采用的内核，后来的 Mozilla Firefox 也采用了相同的内核。

使用该内核的浏览器有：Mozilla Firefox，Mozilla SeaMonkey，Flock(早期版本)，K-Meleon 。

#### Blink 内核

2013 年 4 月 3 日，谷歌在 Chromium Blog 上发表 [博客](http://blog.chromium.org/2013/04/blink-rendering-engine-for-chromium.html)，称将与苹果的开源浏览器核心 `Webkit` 分道扬镳，在 Chromium 项目中研发 `Blink` 渲染引擎（即浏览器核心），内置于 Chrome 浏览器之中。

`Blink` 是一个由 Google 和 Opera Software 开发的浏览器 渲染引擎，Google 计划将这个渲染引擎作为 Chromium 计划的一部分。这一渲染引擎是开源引擎 `WebKit` 中`WebCore` 组件的一个分支，并且在 Chrome（28及往后版本）、Opera（15及往后版本）和 Yandex 浏览器中使用
此外，Mozilla 与三星也达成合作协议开发"下一代"浏览器渲染引擎Servo。

使用该内核的浏览器：Chrome，Opera，多核浏览器。

##### 补充：什么是 Chromium 计划

Chromium 是 Google 为发展自家的浏览器 Google Chrome（以下简称Chrome）而开启的计划，所以 Chromium 相当于 Chrome 的工程版或称实验版（尽管Chrome自身也有β版阶段），新功能会率先在 Chromium 上实现，待验证后才会应用在 Chrome 上，故 Chrome 的功能会相对落后但较稳定。
所以，Chromium 和 Chrome 都是浏览器，Chromium 是开发版本，相比起 Chrome 更新速度快很多，有时数个小时就有一个新版本，不过普通人一般用 Chrome 就可以了。

##### 补充：为什么要搞一个新的 Blink 内核

`Blink` 其实是 `WebKit` 的分支，如同 `WebKit` 是 `KHTML` 的分支。Google 的 Chromium 项目此前一直使用 `WebKit(WebCore)` 作为渲染引擎。

后来，由于苹果推出的 `WebKit2` 与 `Chromium` 的沙箱设计存在冲突，所以 `Chromium` 一直停留在 `WebKit`，并使用移植的方式来实现和主线 `WebKit2` 的对接。这增加了 `Chromium` 的复杂性，且在一定程度上影响了 `Chromium` 的架构移植工作。

基于以上原因，Google 决定从 `WebKit` 衍生出自己的 `Blink` 引擎（后由 Google 和 Opera Software 共同研发），将在 `WebKit` 代码的基础上研发更加快速和简约的渲染引擎，并逐步脱离 `WebKit` 的影响，创造一个完全独立的 `Blink` 引擎。

#### EdgeHTML 内核

Windows10后，IE内置浏览器 Edge 使用的内核。

JS引擎为 `Chakra` 。

使用该内核的浏览器：Edge。

### 国内其他浏览器

上面说到的5大主流浏览器(IE，Edge，Opera，Safari，Firefox，Chrome)，都是单核浏览器。相比较，国内很多浏览器则是双核或多核。即由1个 `Trident` + N个`其他核心` 组成的。

常见的国内浏览器核心如下：

360安全浏览器（1.0-5.0为 `Trident`，6.0为 `Trident+Webkit` ，7.0为 `Trident+Blink` ）
360极速浏览器（7.5之前为 `Trident+Webkit` ，7.5为 `Trident+Blink`）
猎豹安全浏览器（1.0-4.2版本为 `Trident+Webkit`，4.3及以后版本为 `Trident+Blink`）
傲游浏览器（傲游1.x、2.x为 `Trident`，3.x为 `Trident+Webkit`）
世界之窗浏览器（最初为 `Trident`，2013年采用 `Trident+Chromium`）
搜狗高速浏览器（1.x为 `Trident`，2.0及以后版本为 `Trident+Webkit`）
UC浏览器（ `Blink+Trident`）

对于多核浏览器，`Trident` 指 `兼容模式`，而 `其他内核` 指 `高速浏览模式` 。

### 最后

参考文章：

[浏览器内核-渲染引擎、js引擎](https://blog.csdn.net/BonJean/article/details/78453547)

[五大主流浏览器与四大内核](https://blog.csdn.net/yuyanjing123456789/article/details/78689595)

[聊聊浏览器内核](https://segmentfault.com/a/1190000011064695)


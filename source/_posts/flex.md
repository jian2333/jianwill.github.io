---
title: CSS3 中的Flex
date: 2019-05-31 20:20:37
top:
categories:
- 学习笔记
tags:
- web前端
- css
---

CSS3 引入了一种新的布局模式————弹性盒子。

引入弹性盒子的目的是提供一种更加有效的方式来对一个容器中的子元素进行排列、对齐和分配空白空间。

### 简介

弹性盒子由 `弹性容器(Flex container)` 和 `弹性子元素(Flex item)` 组成。`弹性容器` 通过设置 `display` 的值为 `flex` 或 `flex-inline` ，将其定义为 `弹性容器` 。`弹性容器` 内包含一个或多个 `弹性子元素`   。

`弹性容器` 外和 `弹性子元素` 内都是正常渲染，`弹性盒子` 只定义了 `弹性子元素` 如何在 `弹性容器` 内的布局。

`Flex` 即为 `弹性布局`。设置为 `Flex` 布局之后，`子元素` 的 `float`、`clear` 和 `vertical-align` 属性将失效。

接下来，介绍 `Flex` 常用的几个属性。

<!--more-->

### 常用属性

#### flex-direction

`flex-direction` ：`弹性子元素` 在 `弹性容器` 内 **水平方向** 上的排列规则。

**注意，这很重要：**

- `flex-direction` 方向不同( `row` ，`column` )，会导致其他6个 flex 属性( `flex-wrap`，`felx-flow`，`justify-content`，`align-content`，`align-items`，`align-self` )的方向也不同！！
- 后面6个 flex 属性的例子中，`flex-direction` 都为 `row` 。
- 如果 `flex-direction` 改成 `column` ，会导致方向与现在的例子相反。即 `水平xx` 的都会变成 `垂直xx` ，而 `垂直xx` 的会变成 `水平xx` 。 

</br>

主要有以下几个值：`row`、`row-reverse`、`column`、`column-reverse`、`initial`、`inherit` 。

**row**

- **默认值** ；水平方向，从左往右正常排列。
- ![](/images/flex-fd-1.jpg)

**row-reverse**

- 水平方向，从右往左排列。
- ![](/images/flex-fd-2.jpg)

**column**

- 垂直方向，从上往下排列。
- ![](/images/flex-fd-3.jpg)

**column-reverse**

- 垂直方向，从下往上排列。
- ![](/images/flex-fd-4.jpg)

**initial**

- 设置改属性为它的默认值。

**inherit**

- 从父元素继承该属性。

#### flex-wrap

`flex-wrap` ：规定 `弹性子元素` 是一行还是多行显示在 `弹性盒子` 内。即 `弹性子元素` 是否换行。

主要有以下几个值：`nowrap`、`wrap`、`wrap-reverse`、`initial`、`inherit` 。

**nowrap**

- **默认值** ； `弹性子元素` 不换行，宽度会改变(压缩)。
- ![](/images/flex-fw-2.jpg)

**wrap**

- `弹性子元素` 换行，宽度不会改变。
- ![](/images/flex-fw-1.jpg)

**wrap-reverse**

- `弹性子元素` 换行，宽度不会改变，且从右往左排序。
- ![](/images/flex-fw-3.jpg)

**initial**

- 设置该属性为它的默认值。

**inherit**

- 从父元素继承该属性。

#### flex-flow

`flex-flow` ：`flex-direction` 和 `flex-wrap` 的组合，第一个参数为 `flex-direction` ，第二个参数为 `flex-wrap` 。

**row-reverse wrap**

- 反向排序，且换行。
- ![](/images/flex-ff-1.jpg)

#### justify-content

`justify-content` ：`弹性子元素` 关于 `弹性盒子` 垂直轴，在水平方向上的分布规则。

主要有以下几个值：`flex-start`、`flex-end`、`center`、`space-between`、`space-around`、`initial`、`inherit` 。

**flex-start**

- **默认值** ；不拆分，紧贴 `弹性容器` 的左边框。
- ![](/images/flex-jc-1.jpg)

**flex-end**

- 不拆分，紧贴 `弹性容器` 的右边框。
- ![](/images/flex-jc-2.jpg)

**center**

- 不拆分，在 `弹性容器` 内居中显示。
- ![](/images/flex-jc-3.jpg)

**space-between**

- 拆分，分别紧贴 `弹性容器` 的左、右边框， `弹性子元素` 之间由 **空白** 分隔。
- ![](/images/flex-jc-4.jpg)

**space-around**

- 拆分，每个 `弹性子元素` 由宽度相同的 **空白** 围绕。所以 `弹性子元素到边框` 的距离是 `弹性子元素到弹性子元素` 距离到一半。
- ![](/images/flex-jc-5.jpg)

**initial**

- 设置该属性为它的默认值。

**inherit**

- 从父元素继承该属性。

#### align-content

`align-content` ：`弹性子元素` 关于 `弹性盒子` 水平轴，在垂直方向上的分布规则。

主要有以下几个值：`stretch`、`flex-start`、`flex-end`、`center`、`space-between`、`space-around`、`initial`、`inherit` 。

**stretch** ：

- **默认值** ；拆分，`弹性子元素` 被拉伸以适应 `弹性容器` 。
- ![](/images/flex-ac-1.jpg)

**flex-start**

- 不拆分，`弹性子元素` 紧贴 `弹性容器` 的上边框。
- ![](/images/flex-ac-2.jpg)

**flex-end**

- 不拆分，`弹性子元素` 紧贴 `弹性容器` 的下边框。
- ![](/images/flex-ac-3.jpg)

**center**

- 不拆分，在 `弹性容器` 内居中显示。
- ![](/images/flex-ac-4.jpg)

**space-between**

- 拆分，分别紧贴 `弹性容器` 的上、下边框，`弹性子元素` 之间由 **空白** 分隔。
- ![](/images/flex-ac-5.jpg)

**space-around**

- 拆分，每个 `弹性子元素` 由宽度相同的 **空白** 围绕。所以 `弹性子元素到边框` 的距离是 `弹性子元素到弹性子元素` 距离到一半。
- ![](/images/flex-ac-6.jpg)

**initial**

- 设置该属性为它的默认值。

**inherit**

- 从父元素继承该属性。

#### align-items

`align-items` ：设置 `弹性容器` 内的 **所有** 的 `弹性子元素` 的对齐方式。

主要有以下几个值：`stretch`、`flex-start`、`flex-end`、`center`、`baseline`、`initial`、`inherit`

**stretch**

- **默认值** ；拉伸 `弹性子元素` 以适应 `弹性容器` 。
- ![](/images/flex-ai-1.jpg)

**flex-start**

- `弹性子元素` 向上对齐 `弹性容器` 。
- ![](/images/flex-ai-2.jpg)

**flex-end**

- `弹性子元素` 向下对齐 `弹性容器` 。
- ![](/images/flex-ai-3.jpg)

**center**

- `弹性子元素` 居中对齐 `弹性容器` 。
- ![](/images/flex-ai-4.jpg)

**baseline**

- `弹性子元素` 对齐 `弹性容器` 的基线(基本等效于 `flex-start` )。
- ![](/images/flex-ai-5.jpg)

**initial**

- 设置该属性为它的默认值。

**inherit**

- 从父元素继承该属性。

#### align-self

`align-items` ：设置 `弹性容器` 内的 **单个** `弹性子元素` 的对齐方式。

主要有以下几个值：`auto`、`stretch`、`flex-start`、`flex-end`、`center`、`baseline`、`initial`、`inherit` 。

**auto**

- **默认值** ；继承它的父容器 (`弹性容器`) 的 `align-items` 属性，如果没有父容器则该值为 `stretch` 。
- ![](/images/flex-as-1.jpg)

**stretch**

- 拉伸 `弹性子元素` 以适应 `弹性容器` 。
- ![](/images/flex-as-2.jpg)

**flex-start**

- `弹性子元素` 向上对齐 `弹性容器` 。
- ![](/images/flex-as-3.jpg)

**flex-end**

- `弹性子元素` 向下对齐 `弹性容器` 。
- ![](/images/flex-as-4.jpg)

**center**

- `弹性子元素` 居中对齐 `弹性容器` 。
- ![](/images/flex-as-5.jpg)

**baseline**

- `弹性子元素` 对齐 `弹性容器` 的基线(基本等效于 `flex-start` )。
- ![](/images/flex-as-6.jpg)

**initial**

- 设置该属性为它的默认值。

**inherit**

- 从父元素继承该属性。

### 参考链接

[css display:flex 属性](https://www.jianshu.com/p/d9373a86b748)

[CSS flex 属性](https://www.w3cschool.cn/cssref/css3-pr-flex.html)

[CSS3 弹性盒子(Flex Box)](https://www.w3cschool.cn/css3/2h6g5xoy.html)
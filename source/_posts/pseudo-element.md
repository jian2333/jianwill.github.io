---
title: 伪元素的妙用
date: 2020-11-16 10:46:22
top:
categories:
- 学习笔记
tags:
- web前端
- css
---

最近ui优化做的比较多，样式写久了，越来越觉得 `伪元素` 是真的好用，善用 `伪元素` 能极大的提高效率。

下面总结下几种常用的伪元素用法：

- 三角形
- 业务场景下的一些样式
- 垂直居中

<!--more-->

### 用法

#### 三角形

在 `css` 中，`border边框` 是由**上下左右四个三角形**组成的，所以，**设置 `border` 边框为 `transparent` ，再设置要显示的 `border` 颜色**， 即可以显示出三角形。

- 通过设置不同的 `border` 和 `border-left` 宽度，可以生成不同效果的三角形
- 添加 `border-radius` ， 可以让 **三角形变成扇形**

**Demo** 

![](/images/pe-1.jpg)

**相关代码**

```html
<div class="pseudo">
  <div class="triangle1"></div>
  <div class="triangle2"></div>
  <div class="triangle3"></div>
  <div class="triangle4"></div>
</div>
```

```less
.pseudo {
  position: relative;
  top: 10px;
  width: 100%;
  height: 150px;
  border: 1px solid red;
  display: grid;
  grid-template-columns: repeat(auto-fill, 120px);
  justify-items: center;
  align-items: center;
  .triangle1 {
    position: relative;
    width: 100px;
    height: 100px;
    &:after {
      content: '';
      border-top: 40px solid green;
      border-right: 40px solid orange;
      border-left: 40px solid red;
      border-bottom: 40px solid blue;
      position: absolute;
      top: 10px;
      left: 10px;
    }
  }
  .triangle2 {
    position: relative;
    width: 100px;
    height: 100px;
    &:after {
      content: '';
      border: 40px solid transparent;
      border-left: 40px solid red;
      position: absolute;
      top: 10px;
      left: 10px;
    }
  }
  .triangle3 {
    position: relative;
    width: 100px;
    height: 100px;
    &:after {
      content: ''
      border: 40px solid transparent;
      border-left: 80px solid red;
      position: absolute;
      top: 10px;
      left: 10px;
    }
  }
  .triangle4 {
    position: relative;
    width: 100px;
    height: 100px;
    &:after {
      content: '';
      border: 40px solid transparent;
      border-left: 40px solid red;
      border-radius: 50%;
      position: absolute;
      top: 10px;
      left: 10px;
    }
  }
}

```



#### 业务场景

最近的项目中，有个需求是**自适应的情况下，显示5列图标，每列之间用箭头连接**。

单独的图标自适应好做，`flex` 、 `grid` 布局都可以实现，但是图标间的 `连接箭头` 就不好做了，因为2个图标之间的距离，是不固定的....**但是！如果用伪元素，这个问题就很容易解决了～**

**Tips:** 

- 1个 `元素` 只能有1个 `伪元素` ，如果要使用多个 `伪元素` ，就得用多个 `元素div`
- `arrows-before.width` 宽度多  `10px` ，是为了能和  `arrows-after`  重叠，视觉看起来虚线不断裂

**Demo**

![](/images/pe-2.jpg)

**相关代码**

```vue
<div class="home-menu">
  <div :class="[{'arrows-after': arrowsAfter}, {'arrows-before': arrowsBefore}]"></div>
  <div :class="{'arrows': arrowsBefore}"></div>
  <div class="title" @click="clickIcon">
    <div class="bg" :style="bgStyles"></div>
    <span class="sp">{{title}}</span>
  </div>
  <div class="body">
    <div v-for="(item, index) in values" :key="index" :class="bodyClass" :style="bodyStyle">
      <p class="value">{{item.value}}</p>
      <p class="name">{{item.name}}</p>
    </div>
  </div>
</div
```

```less
@arrows-border: #E6E6E6;
@arrows-margin: 24px;
@arrows-width: 8px;
@bg-r: 60px;

.arrows(@arrows-margin) {
  .arrows-before:before {
    content: '';
    width: calc(~'50% - @{bg-r} / 2 - @{arrows-margin} - @{arrows-width} + 10px');
    height: 0;
    border: 1px dashed @arrows-border;
    position: absolute;
    left: -10px;
    top: 30px;
  }
  .arrows:after {
    content: '';
    width: 0;
    height: 0;
    border: @arrows-width / 2 solid transparent;
    border-left: @arrows-width solid @arrows-border;
    position: absolute;
    left: calc(~'50% - @{bg-r} / 2 - @{arrows-margin} - @{arrows-width}');
    top: 27px;
  }
  .arrows-after:after {
    content: '';
    width: calc(~'50% - @{bg-r} / 2 - @{arrows-margin} + @{arrows-width}');
    height: 0;
    border: 1px dashed @arrows-border;
    position: absolute;
    right: -@arrows-width;
    top: 30px;
  }
}
.arrows(@arrows-margin);
@media only screen and (max-width: 1600px) {
  @arrows-margin: 12px;
  .arrows(@arrows-margin);
}
```



#### 垂直居中

参考之前写的一篇[文章](https://jian2333.github.io/2019/06/22/centered/)，利用 `父元素` 的 `伪元素` ，可以实现垂直居中

**相关代码**

```html
<div class="parent">
  <div class="centered">
    我是居中文字
  </div>
</div>
```

```css
.parent {
  width: 400px;
  height: 300px;
  border: 1px soild pink;
}
.centered: {
  width: 100px;
  height: 60px;
  border: 1px solid green;
  display: inline-block;
  vertical-align: middle;
}
.parent::before {
  content: '';
  width: 0;
  height: 100%;
  background: #ccc;
  display: inline-block;
  vertical-align: middle;
}
```

### 总结

伪元素的用法还有很多，目前常用的暂时就这些，其他用法等用的多后再记录下来吧～
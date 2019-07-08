---
title: 重排重绘 与性能优化
date: 2019-06-23 14:46:43
top:
categories:
- 学习笔记
tags:
- web前端
- 性能优化
- css
- js
---

性能优化是个老生常谈的话题了，重排重绘也是性能优化的一种。

减少或避免重排重绘，能有效地提升浏览器性能。

那么，如何减少或避免重排重绘呢？

本文将从 `css` 和 `js` 两个角度来分析 ～

<!--more-->

### 概念

![](/images/rr-1.jpg)

#### 重排

- 重排又叫 `reflow` ，指的是为了重新渲染部分或整个页面，重新计算页面元素的 **位置** 和 **几何结构** 的进程。
- 简单来说，就是当元素改变的时候，将会影响文档内容或结构，或元素位置，此过程叫做 `reflow` 。
- `reflow` 是导致 `DOM脚本` 执行效率低下的关键因素之一。页面上任何一个节点触发了 `reflow` ，都会导致它的 **子节点** 和 **祖先节点** 重新渲染。

**那么，什么时候会导致 reflow 发生呢**

- 改变窗口大小
- 改变文字大小
- 添加/删除样式表
- 内容的改变，（用户在输入框中输入内容也会）
- 激活伪类，如 `:hover` 
- 操作 `class` 属性
- 脚本操作 `DOM` 
- 计算 `offsetWidth` 和 `offsetHeight`
- 设置 `style` 属性

| 常见的重排元素 |              |                |            |
| -------------- | ------------ | -------------- | ---------- |
| width          | height       | padding        | margin     |
| display        | border-width | border         | top        |
| position       | font-size    | float          | bottom     |
| overflow-y     | font-weight  | over           | left       |
| font-family    | line-height  | vertical-align | right      |
| clear          | white-space  | text-align     | min-height |

#### 重绘

- 重绘又叫 `repaint` ，指的是当元素改变时，将不会影响元素在当前页面中的位置（比如 `background-color`，`border-color`，`visibility`），而仅仅会应用新的样式重绘此元素的过程。

**那么，什么时候会发生 repaint 呢**

| 常见的重绘元素  |                  |                     |                   |
| --------------- | ---------------- | ------------------- | ----------------- |
| color           | border-style     | visibility          | background        |
| text-decoration | background-image | background-position | background-repeat |
| outline-color   | outline          | outline-style       | border-radius     |
| outline-width   | box-shadow       | background-size     |                   |

### js方面的优化

js方面主要是从 `DOM` 角度来优化重排重绘。包括以下几方面：

- 文档片段
- 使用 `innerHTML`
- 服务端渲染
- 使用事件代理
- 减少 `HTMLCollection` 访问
- 尽量不修改影响比较大的 `DOM`
- 函数节流

**文档片段**

- 在多次操作 `DOM` 时，比如给一个 `<ul>` 元素添加多个 `<li>` 时，每添加一次 `<li>` 都会进行一次 `现场更新(即reflow)` 。

- 使用 `文档片段` 的话，只会进行一次 `现场更新(reflow)` 。

- 使用 `appendChild()` 时，只有 `文档片段` 的 `子节点` 会添加到目标，`文档片段` 本身不会被添加到目标。

  ```js
  var list = document.getElementById('myList'),
      fragment = document.createDocumentFragment(),
      item,
      i;
  for (i=0;i<10;i++) {
    item = document.createElement('li');
    fragment.appendChild(item);
    item.appendChild(document.createTextNode('item' + i));
  }
  list.appendChild(fragment);
  ```

**使用 innerHTML**

- 先拼接好要渲染的 `html字符串` 。

- 然后使用 `innerHTML` 进行一次渲染。

  ```js
  var list = document.getElementById('myList'),
      html = '',
      i = 0;
  for (i=0;i<10;i++) {
    html += '<li>item' + i + '</li>/br';
  }
  list.innerHTML = html;
  ```

**服务端渲染**

- 和 **使用 innerHTML** 类似，只是服务端会返回 **拼接好的 html字符串** ，而不需要前端拼接 `html字符串` 了。
- 然后直接使用 `innerHTML` 进行一次渲染即可。

**使用事件代理**

- 《JavaScript 高级程序设计》P675。
- 页面上的 `事件处理程序的数量` 和 `页面响应用户交互的速度` 呈负相关。所以，如果减少 `事件处理程序的数量` ，那 `响应速度` 将会更快。
- 对于同一个事件，可以在 `对应DOM` 上定义，也可以在 `父元素` 上定义一大片事件，后者则是事件代理。

**减少 HTMLCollection 访问**

- 任何时候访问 `HTMLCollection` 都很耗资源。

- 所以可以把要访问的 `HTMLCollectionp[i]` 存放到一个变量里，之后要用时直接访问变量。

  ```js
  var images = document.getElementsByTagName('img'),
      image,
      i,
      len;
  for (i=0,len=images.length;i<len;i++) {
    image = images[i];
    // 一些操作
  }
  ```

**尽量不修改影响比较大的 DOM**

- 尽可能限制 `reflow` 的影响范围，尽可能在低层级的 `DOM` 上进行操作。

**函数节流**

- `函数节流` 的基本思想是，不能在没有间断的情况下(比如 `onresize`)连续重复执行某些代码，因为这可能导致浏览器崩溃。

- 实现过程：第一次调用函数时，会创建一个 **定时器** ，在指定的时间间隔之后再执行代码；第二次调用该函数时，会先清除前一次的 **定时器** 并 **设置另一个** 。

- 目的是 只有在执行函数的请求停止了一段时间后才执行(新函数) 。

  ```js
  // 未使用函数节流
  // 频繁的更新DOM，可能导致浏览器崩溃
  window.onresize = function() {
    var div = document.getElementById('myDiv');
    div.style.height = div.offsetWidth + 'px';
  }
  ```

  ```js
  // 使用函数节流
  // 只有前面函数停止一段时间后，才会执行第二个函数
  // 提高了性能
  function throttle(method, context) {
    clearTimeout(method.Id);
    method.Id = setTimeout(function(){
      method.call(context);
    }, 100);
  }
  function resizeDiv() {
    var div = document.getElementById('myDiv');
    div.style.height = div.offsetHeight + 'px';
  }
  window.onresize = function() {
    throttle(resizeDiv);
  }
  ```

### css方面的优化

`css` 方面的优化主要是指 `非DOM` 方面的优化。包括以下几方面：

- 尽量使用 `className` 来一次修改样式，而不是高频的每次只修改一条样式。
- 多次修改 `DOM` 样式时，可以使用 `先改成display:none --> 修改n次样式 --> 最后改成display:xxx` 。
- 为动画的元素使用绝对定位 `absolute` 或 `fixed` ，减少附近元素的重排重绘。
- 尽量少使用 `table` 。因为 `table` 一个单元格样式的改变，会导致整个 `table` 重排重绘，性能消耗巨大。
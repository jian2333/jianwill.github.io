---
title: 严格模式 的特别之处
date: 2019-06-13 09:55:09
top:
categories:
- 学习笔记
tags:
- web前端
- js
---

**严格模式** 是 `ECMAScript5` 引入的一个概念。通过严格模式，可以在函数内部选择进行较为严格的全局或局部的错误条件检测。使用严格模式的好处是可以提早知道代码中存在的错误，及时捕获一些可能导致编程错误的 `ECMAScript` 行为。

那么，**严格模式** 和 **非严格模式** 有什么区别呢？

我们一起往下看 ～ 

<!--more-->

### 如何使用

- 使用 `"use strict";` (注意后面的分号) ；
- 支持 `严格模式` 的引擎会启动这种模式，不支持 `严格模式` 的引擎会忽略这个 **编译指示** ；
- 可以写在全局作用域(函数外部)，也可以写在函数内部。建议写在函数内部；

### 区别

**1. 不允许意外创建变量**

- ```js
  // 未声明变量
  // 非严格模式模式：创建全局变量
  // 严格模式：抛出 ReferenceError
  message = "Hello world";
  ```

**2. 不能对变量调用delete操作符**

- ```js
  // 删除变量
  // 非严格模式：忽略，静默失败
  // 严格模式：抛出 ReferenceError
  var color = "red";
  delete color;
  ```

**3. 变量名限制**

- 不能使用 `implements`、`interface`、`let`、`package`、`private`、`protected`、`public`、`static`、`yield` 作为变量名；
- `非严格模式` 下可使用；

**4. 操作对象限制**

- 一般来说，`非严格模式` 下会静默失败，即忽略；`严格模式` 下会抛出错误；

- 以下情况操作对象的属性会导致错误( `严格模式` 下) ：

  - 为 `只读属性` 赋值会抛出 `TypeError` ；
  - 对 `不可配置` 的属性使用 `delete` 操作符会抛出 `TypeError` ；
  - 对 `不可扩展` 的对象添加属性会抛出 `TypeError` ；
  - 使用 `对象字面量` 时，属性名要唯一；

  ```js
  // 重名属性
  // 非严格模式：没有错误，以第二个为准
  // 严格模式：抛出语法错误
  var person = {
    name: "Nicholas",
    name: "Greg"
  };
  ```

**5. 函数参数唯一**

- `严格模式` 下要求命名函数的参数名必须唯一；

  ```js
  // 重名参数
  // 非严格模式：没有错误，只能访问第二个参数
  // 严格模式：抛出语法错误
  function sum(num, num) {
    // do something
  }
  ```

**6. 修改参数的值不会反映到 arguments**

- `非严格模式` 下修改参数的值会反映到 `arguments` 对象中，而 `严格模式` 不会，因为它们是完全独立的；

  ```js
  // 修改命名参数的值
  // 非严格模式：修改会反映到 arguments 中
  // 严格模式：修改不会反映到 arguments 中
  function showValue(value) {
    value = "Foo";
    alert(value);		// "Foo"
    alert(arguments[0]);	// 非严格模式："Foo"
                          // 严格模式："Hi"
  }
  showValue("Hi");
  ```

**7. 淘汰了 arguments.callee 和 arguments.caller**

- ```js
  // 访问 arguments.callee
  // 非严格模式：没有问题
  // 严格模式：抛出 TypeError
  function factorial(num) {
    if (num <= 1) {
      return 1;
    } else {
      return num * arguments.callee(num - 1);
    }
  }
  var result = factorial(5);
  ```

**8. 函数名限制**

- 不能使用 `implements`、`interface`、`let`、`package`、`private`、`protected`、`public`、`static`、`yield` 作为函数名；
- `非严格模式` 下可使用；

**9. if 内不能声明函数**

- `严格模式` 下，只能在脚本的顶级和函数内部声明函数，也就是说，在 if 语句中声明函数会导致语法错误；

  ```js
  // 在 if 语句中声明函数
  // 非严格模式：将函数提升到 if 语句外部
  // 严格模式：抛出语法错误
  if (true) {
    function doSomething() {
      //...
    }
  }
  ```

**10. eval 在包含的上下文中不能再创建变量和函数**

- `严格模式` 下，`eval` 内声明的变量只在 `eval` 表达式内部有效；

  ```js
  // 使用 eval() 创建变量
  // 非严格模式：弹出对话框显示 10
  // 严格模式：调用 alert(x) 时会抛出 ReferenceError 
  function doSomething() {
    eval("var x=10");
    alert(x);
  }
  ```

  ```js
  // 严格模式：eval() 声明的变量只在 eval 表达式内部有效
  function doSomething() {
    var result = eval("var x=10, y=11, x+y");
    alert(result);	// 21
  }
  ```

**11. 禁止使用 eval 和 arguments 作为标识符，也不能修改它们的值**

- `严格模式` 下禁止使用 `eval` 和 `arguments` 作为标识符，也不允许读取它们的值。

- 以下几种方式都会抛出错误：

  - 使用 `var` 声明；
  - 赋予另一个值( `arguments="xx"` 整体赋值会报错，`arguments[0]="yy"` 单独赋值不会报错)；
  - 尝试修改包含的值，如使用 `++` (也相当于整体赋值了) ；
  - 用作函数名；
  - 用作命名的函数参数；
  - 在 `try-catch` 语句中用作例外名；

  ```js
  // 把 eval 和 arguments 作为变量引用
  // 非严格模式：不报错
  // 严格模式：抛出语法错误
  function doSomething(){
    var eval = 10;
    var arguments = "Hello world!";
  }
  ```

**12. 抑制this**

- 在 `非严格模式`  下使用 `call()` 和 `apply()` 方法时，`null` 和 `undefined` 会自动转换为 **全局对象** ；而在 `严格模式` 下，函数的 `this` 的值始终是指定的值；

  ```js
  // 访问属性
  // 非严格模式：访问全局属性
  // 严格模式：抛出错误，因为 this 的值为 null
  var color = "red";
  function displayColor() {
    alert(this.color);
  }
  displayColor.call(null);
  ```

**13. 抛弃了 with 语句**

- `非严格模式` 下使用 `with` 能够改变解析标识符的路径，`严格模式` 下使用 `with` 会导致语法错误；

  ```js
  // with 的语句用法
  // 非严格模式：允许
  // 严格模式：抛出语法错误
  with(location) {
    alert(href);
  }
  ```

**14. 去掉了八进制字面量**

- `严格模式` 下，八进制字面量已经成为无效的语法了，会抛出异常；

  ```js
  // 使用八进制字面量
  // 非严格模式：值为8
  // 严格模式：抛出语法错误
  var value = 010;
  ```

- 在使用 `parseInt()` 时，`严格模式下` 八进制字面量会被当作以 0 开头的十进制字面量；

  ```js
  // 使用 parseInt() 解析八进制字面量
  // 非严格模式下：值为8
  // 严格模式下：值为10
  var value = parseInt("010");
  ```

  
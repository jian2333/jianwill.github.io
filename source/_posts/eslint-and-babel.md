---
title: eslint && babel
date: 2020-06-24 14:28:22
top:
categories:
- 学习笔记
tags:
- web前端
---

关于`eslint`和`babel`的一点理解：

`eslint`: 代码语法校验，主要是语法的使用是否正确，附带也可以格式化代码风格。

`babel`: 转义代码，将浏览器不支持的新语法，转义成浏览器支持的语法。所以使用`babel`，我们就可以 **毫无顾忌** 的使用新语法了。

但有时候吧，`eslint` 校验新语法时识别不了，就会报错了，这时候`babel-eslint`就登场了。

`babel-eslint`: 将`eslint`无法识别的新语法转义成`eslint`可以识别的语法，进而通过`eslint`校验。`babel-eslint`需搭配`eslint`一起使用。

题外话：

- `babel`有多种引入方式，最新的推荐方式是 **按需转义**
- 格式化(统一)代码风格，还可以使用以下2种方式。主要是改变代码的显示风格，并不会对代码的`语法规则`做校验：
  - `.editorconfig`: 本地配置文件，一般用于`换行、缩进等`。
  - `.prettierrc`：当前主流的方法。所有规则`基本都可配置`。搭配 `webstorm file watch` 可实现 **保存时自动格式化/提交代码时自动格式化**
---
title: Promise 相关简介
date: 2019-06-14 20:30:25
top:
categories:
- 学习笔记
tags:
- web前端
- js
---

`promise` 是 `ECMAScript6` 新加的一个概念。

本文将使用简洁的文字介绍 `promise` ，仅介绍大概情况。

更多详情请参考结尾的推荐文章。

好了，正文开始 ！

<!--more-->

### 简介

`Promise` 是抽象 `异步操作` 对象以及对其进行一系列操作的组件。

`Promise` 最大的功能是把 `异步操作` 变为 `同步操作` 。即只有 `异步操作` 完成后，才进行后面的操作。

`Promise` 定义了一组 `上述操作` 相关的接口和方法，直接使用即可。

**Promise 状态**

使用 `new promise` 实例化的 `promise对象` 有以下三个状态：

**Fulfilled**

`resolve(成功)` 时，此时会调用 `onFulfilled` 。

**Rejected**

`reject(失败)` 时，此时会调用 `onRejected` 。

**Pending**

既不是 `resolve` 也不是 `reject` 状态，也就是 `promise` 对象刚被创建后的初始化状态。

### 方法

**创建 promise 实例**

创建实例有三种方法：

- `new Promise()` ；

- `Promise.resolve` ；

- `Promise.reject` ；

  ```js
  // 方法1
  var promise1 = new Promise(function(resolve, reject) {
    resolve('11');
    resolve(new Error('22'));
  });
  
  // 方法2
  var promise2 = Promise.resolve('1');
  // 等效于
  var promise2 = new Promise(function(resolve) {
    resolve('1');
  });
  
  // 方法3 
  var promise3 = Promise.reject('2');
  // 等效于
  var promise3 = new Promise(function(null, reject) {
    reject('2');
  });
  ```

**resolve**

- `Promise.resolve` ：创建实例；
- 返回 `promise` 对象，主要分下面3种情况：
  - 接收参数为 `promise` 对象：返回的还是接收到的 `promise` 对象；
  - 接收参数为 `thenable` 类型的对象：返回一个新的 `promise` 对象，这个对象有具有一个 `.then` 方法。更多 `thenable` 的信息，[点我查看](http://liubin.org/promises-book/#ch2-promise-resolve)  ；
  - 接收参数为 `其他类型` ：返回一个将该对象作为值的新的 `promise` 对象；

**reject**

- `Promise.reject` ：创建实例；
- 返回一个使用了接收到的值进行了 `reject` 的新的 `promise` 对象；
- 传给 `Promise.reject` 的值应该是一个 `Error` 类型的对象；
- 另外，和 `Promise.resolve` 不同的是，即使 `Project.reject` 接收的是一个 `promise` 对象，该函数也还是会返回一个全新的 `promise` 对象；

**then**

- `promise.then` ：`promise` 中最常用的方法；

- 在 `promise` 对象变为 `resolve` 或 `reject` 后，执行相应的回调函数；

- `then` 中 `return` 的值，作为参数传入 `链式方法` 里的下一个方法；

  ```js
  // 常规用法
  var promise = new Promise(function(resolve, reject) {
    //...
    resolve('11');
    reject(new Error('Boom!'));
  });
  promise.then(function(value) {
    console.log(value)	// 11
  }, function(error) {
    console.log(error);
  })
  
  // 链式调用 return 的值作为下一个方法的参数
  // 见后面 链式方法
  ```

**catch**

- `promise.catch` ：处理异常的方法，和 `reject` 类似；

- 包含 `promise` 对象变为 `reject` 、手动 `throw new Error()` 、`系统抛出异常` 三种情况；

- 推荐使用 `catch` ，因为对于 `then(f1,f2).catch()` 这种情况，如果 `f1` 抛出异常，`f2` 是捕获不了的，链式方法后面的 `catch` 才能捕获到，或者改成 `then(f1,f2).then(null,f3)` 这样，`f3` 也能捕获到；

  ```js
  // 常规用法
  var promise = Promise.reject(new Error("BOOM!"));
  promise.catch(function(value) {
    console.log(value);		// Error: BOOM!
  })
  
  // 等效于 reject
  promise.then(function(value){
    console.log(value);
  },function(error){
    console.log(error);
  })
  // 等效于下面的写法，推荐用下面的写法
  promise.then(function(value){
    console.log(value);
  }).catch(function(error){
    console.log(error);
  })
  
  // 推荐用 catch 的原因： reject 和 catch 都只能捕获上一级的错误
  // 如果用 reject 的话，同级的 resolve 中抛出异常的话，reject 就不能捕获了
  // 而如果用 catch 话，因为 catch 是链式方法的下一级，所以可以捕获到上一级 resolve 中的异常
  // 用链式方法 + reject 的话也行，效果和 catch 一样
  // 下面的写法，和上面的 catch 方法效果一样，不过一般不这样写，一般用 catch
  promise.then(function(value){
    console.log(value)
  }).then(null, function(error){
    console.log(error);
  })
  ```

**all**

- `Promise.all` ：传入一个 `promise` 对象的数组作为参数，只有当数组里的 **所有**  `promise` 对象全部变为 `resolve` 或 `reject` 时，才会调用 `then` 里相应的回调函数方法；

  ```js
  var promise1 = new Promise(function(resolve, reject){
    //...
    resolve('1');
    reject(new Error('Boom!'));
  });
  var promise2 = new Promise(function(resolve, reject){
    //...
    resolve('2');
    reject(new Error('Boom!'));
  });
  var promise3 = new Promise(function(resolve, reject){
    //...
    resolve('3');
    reject(new Error('Boom!'));
  });
  
  var promises = [promise1, promise2, promise3];
  Promise.all(promises).then(function(value){
    console.log(value);		// ["1", "2", "3"]
  }).catch(function(error){
    console.log(error);
  });
  ```

**race**

- `Promise.race` ：和 `Promise.all` 类似，区别是 **只要有一个** `promise` 对象变为 `resolve` 或 `reject` 时，就会调用 `then` 方法；

  ```js
  var promise1 = new Promise(function(resolve, reject){
    //...
    resolve('1');
    reject(new Error('Boom!'));
  });
  var promise2 = new Promise(function(resolve, reject){
    //...
    resolve('2');
    reject(new Error('Boom!'));
  });
  var promise3 = new Promise(function(resolve, reject){
    //...
    resolve('3');
    reject(new Error('Boom!'));
  });
  
  var promises = [promise1, promise2, promise3];
  Promise.race(promises).then(function(value){
    console.log(value);		// 1
  }).catch(function(error){
    console.log(error);
  });
  ```

### Advanced

**catch 和 reject 是一样的**

- 上面已经说过，`catch` 和 `reject` 是一样的，只是写法不同；
- 不过从 `链式方法` 的写法角度来说，`catch` 写在下一级，能够捕获上一级中 `reject` 无法捕获的 `resolve` 中的异常，所以，推荐使用 `catch` 写法；

**thenable**

- `thenable` 指的是一个具有 `.then` 方法的对象；
- `Promise.resolve` 可以将 `thenable` 对象转换为 `promise` 对象，返回的是一个新的 `promise` 对象；
- 更多 `thenable` 的信息，[点我查看](http://liubin.org/promises-book/#ch2-promise-resolve) ；

**链式方法**

- 由于 `then` 和 `catch` 都返回了一个新的 `promise` 对象，因此它们可以用 `.` 的方式进行链式调用；

- `then` 返回的是新的 `promise` 对象，`then` 里面的方法返回的是 链式方法中下一个方法的传入参数；

- 更多详情，[点我查看](http://liubin.org/promises-book/#then-return-new-promise) ；

  ```js
  // then 返回的 bpromise 是一个 promise 对象
  // then 里面函数返回的 value1*2 是链式方法中下一个方法的传入参数(value2)
  var apromise = new Promise(function(resolve) {
    resolve(100);
  });
  var bpromise = apromise.then(function(value1){
    return value1 * 2;
  });
  bpromise.then(function(value2) {
    console.log(value2);	// 200
  })
  ```

### 示例

**使用 ajax + promise 来读取并操作数据**

- ```js
  function getURL(URL) {
    return new Promise(function(resolve, reject) {
      var xhr = new XMLHttpRequest();
      xhr.open('GET', URL, true);
      xhr.onload = function () {
        if (xhr.status === 200) {
          resolve(xhr.responseText);
        } else {
          reject(new Error(xhr.statusText));
        }
      };
      xhr.onerror = function () {
        reject(new Error(xhr.statusText));
      };
      xhr.send();
    });
  }
  // 运行示例
  var url = "http://httpbin.org/get";
  getURL(url).then(function onFulfilled(value) {
    console.log(value);
  }).catch(function onRejected(error) {
    console.log(error);
  });
  ```

### 更多资料

[[推荐]JavaScript Promise迷你书（中文版）](http://liubin.org/promises-book/)

[快来使用ES2015的Promise吧](https://www.w3cschool.cn/vnpqd/vnpqd-sdlo25sr.html)

[ES6 Promise 对象](https://www.runoob.com/w3cnote/es6-promise.html)

[JavaScript Promise 对象](https://www.runoob.com/w3cnote/javascript-promise-object.html)
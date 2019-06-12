---
title: 《JavaScript 高级程序设计》总结
date: 2019-06-12 09:49:39
top:
categories:
- 学习笔记
tags:
- web前端
- js
---

终于把《JavaScript 高级程序设计》看完(假装看完，其实跳过了几章 0.0)了，颇有一些心得，总结一下。

首先就是，这本书的内容，大而全。Js的各方面都有介绍，内容详细，涉及面广。

其次，有些内容相对较旧了。毕竟 `第三版` 是2012年3月发行的，当时 `ES6` 还没定稿，所以这本书后面有些内容，和现在会稍有不同，主要是最后几章。

接下来，是看完本书后的部分总结。

<!--more-->

## 心得

### JS加载

JS会阻塞 DOM 的加载和渲染。

`defer` ：

- 立即加载JS，但不执行，待DOM渲染完成后再执行JS。
- 多个 `defer` 按顺序执行，但实际上不一定，所以建议只有1个JS使用 `defer` 参数。
- `XHTML` 下需写成 `defer="defer"` 。

`async` ：

- 异步加载JS(不阻塞DOM)，加载完后执行JS(此时会阻塞DOM)，执行完后继续加载/渲染DOM。
- 不按顺序执行。
- `XHTML` 下需写成 `async="async"` 。

![](/images/pj-1.jpg)

### 类型检测

**基本类型** ：

- `typeof` 。
- 包括 `undefined`、`number`、`boolean`、`string` ，外加 `function` 。

**引用类型** ：

- `instanceof` 。
- 包括 `Object`、`Array`、`Date`、`Regexp` 。
- 检测 `Object` 永远返回 `true` ，检测 `基本类型` 永远返回 `false` 。

```js
// 基本类型
var str = "string";
alert(typeof(str));			// string

// 引用类型
var person = {};
alert(person instanceof Object);	// true
```

### 垃圾收集

垃圾收集主要有 `标记清除` 和 `引用计数` 两种方式，其中 `标记清除` 是现在的主流方式。

**标记清除** ：

- 现在5大主流浏览器使用的方式。
- 当一个变量进入一个环境(比如函数)时，将该变量标记为 `进入环境` ，当该变量离开环境(比如函数)时，标记该变量为 `离开环境` 。
- 垃圾收集器会给存储在内存上的所有变量都加上标记，然后去掉环境中的变量以及被环境中的变量引用的变量的标记。而在此之后再被加上标记的变量将被视为准备删除的变量。最后垃圾收集器销毁那些带标记的值并回收它们所占用的空间。
- 不同浏览器垃圾收集的时间间隔不同。

**引用计数** ：

- 含义是：跟踪记录每一个值被引用的次数。
- 当一个值被一个变量引用时，该值的 `引用次数` +1，又被另一个变量引用时，`引用次数` 继续+1，取消引用也类似。
- 当 `引用次数` 为0后，当 `垃圾收集器` 下次运行时，就会释放这些 `引用次数` 为0的值所占用的内存。
- 这个方法有个严重的 bug ，当循环引用的时候，引用次数就永远不会为0，垃圾收集器也就 **永远不会回收这些循环引用的值/变量** 。
- 可以使用 `variable = null` 这样的方式 **手动清除引用** 。

### 函数

**call，apply** ：

- 第一个参数是 `this` 的值，同时也是这个函数的环境/作用域；第二个参数是传入函数的参数；

- 利用第一个参数，可以改变函数执行的环境/作用域，**这是最常用且非常巧妙的一种用法！！**；

  ```js
  age = 10;
  var obj = { name: 'Jack', age: 18};
  var fun1 = function(num) {
    alert(this.age + num);
  }
  
  // 传入null
  fun1.apply(null, [1]);	// 非严格模式下，this为window；严格模式下，this为null，然后会报错。
  // 传入this
  fun1.apply(this, [1]);	// this为window，全局作用域，返回11
  // 传入obj
  fun1.apply(obj, [1]);	// this为obj对象，obj作用域，返回19
  ```

**arguments.callee()** ：

- 指向当前函数，一般 **递归函数** 中使用较多。

- `严格模式` 下不能使用。

  ```js
  // 阶乘函数
  function factorial(num) {
    if (num <= 1) {
      return 1;
    } else {
      return num * factorial(num - 1);
    }
  }
  
  //可以改写成以下写法，减少函数的执行与函数名的耦合
  function factorial(num) {
    if (num <= 1) {
      return 1;
    } else {
      return num * arguments.callee(num - 1);	// 此行改动
    }
  }
  ```

**arguments.caller() **：

- 指向调用该函数的函数，返回调用函数的源代码。

- `严格模式` 下不能使用。

  ```js
  function outer() {
    inner();
  }
  function inner() {
    alert(arguments.callee.caller);
  }
  outer();	// 返回outer函数的源代码
  ```

### JSON

**JSON类型**

**简单值** ：

- 包含 `字符串`、`数值`、`布尔值` 和 `null` ( `undefined` 会自动被忽略 )。
- `JSON字符串` 和 `JS字符串` 的区别：
  - `JSON字符串` 必须使用双引号，`JS字符串` 可以使用 `双引号` 和 `单引号` ；

**对象** ：

- `JSON对象` 和 `JS对象` 的区别：
  - `JSON对象` 的属性名必须使用 `双引号` ，`JS对象` 的属性名 `可以加双引号也可以不加引号` ；
  - `JSON对象` 没有声明变量；
  - `JSON对象` 末尾没有 `分号` ；

**数组** ：

- `JSON数组` 和 `JS数组` 的区别：
  - `JSON数组` 没有声明变量；
  - `JSON数组` 末尾没有 `分号` ；

**JSON.stringify(obj, para1, para2)** ：

- `JS对象` => `JSON字符串` ；
- **para1** ：可选，用于过滤结果，可以是数组或函数；
  - 数组 ：只返回数组中的 `key` ；
  - function(key, value) ：重新格式化每个 `key` 的值；
- **para2** ：可选，用于指定缩进，可以是数值或字符串；
  - 数值：缩进 n 个空格，最大长度为10；
  - 字符串：缩进字符串，最大长度为10；

**JSON.parse(str, para1)** ：

- `JSON字符串` => `JS对象` ；
- **para1** ：可选，和 `JSON.stringify` 的 `para1` 中的 `fucntion` 类似，用于还原结果，是一个函数；
  - function(key, value) ：重新格式化每个 `key` 的值；

### Ajax

`Ajax` 是一种局部刷新页面的技术，它向服务器请求数据而无需卸载页面，只局部刷新页面。

#### **原生写法**

```js
var xhr = new XMLHttpRequest();
xhr.open(type, url, isAsync);
xhr.send(data);
```

**说明**：

- **type** ：`post` 、`get` 等；

- **url** ：请求的 url ，`get` 请求时查询字符串参数必须使用 `encodeURIComponent()` 进行编码；

- **isAsync** ：是否异步。`jQuery` 默认为 `true` ；

- **data** ：一定要填，为空的话写 `null` ；

- 可以使用表单形式给服务器传数据(结果是键值对，而不是string字符串)，使用方法如下：

  ```js
  // 初始化
  // ...
  
  // 方法1
  var form = document.getElementById("user-info");
  var data = new FormData(form);
  // 方法2
  var data = new FormData();
  data.append("key1", "value1");
  data.append("key2", "value2");
  // 发送给服务器
  xhr.send(data);
  ```

**更多信息** ：

- Page 575；
- **HTTP头信息** ：可以自定义信息在头部，供服务器读取；
- **超时设定** ：`xhr.timeout` 、`xhr.ontimeout=function(){}` ；
- **进度事件** ：`load` 、`progress` ；

#### **跨域介绍**

`CORS(Cross-Origin-Resource Sharing)` 定义了跨域时，浏览器与服务器该如何通信。

`CORS` **的基本思路**

- `浏览器` => `服务器` 的 `请求头` 中添加：`Origin: url` ；
- `服务器` => `浏览器` 的 `响应头` 中添加： `Access-Control-Allow-Origin: url` ；
- 这2个 `url` 必须一致；

**IE8 的跨域**

- 使用 `XDR(XDomainRequest)` 对象来创建实例(替代 `XMLHttpRequest` 对象)；

  ```js
  var xdr = new XDomainRequest();
  xdr.open(type, url, isAsync);
  xdr.send(data);
  ```

**其他主流浏览器的跨域**

- **原生支持** ：直接使用 `XMLHttpRequest` 对象创建实例即可；

**其他跨域技术**

- **图像Ping** ：单向，`浏览器` => `服务器` ；
- **JSONP** ：双向，把 url 添加到 `<script>` 标签中，然后 `<script>` 标签添加到 `document.body` 内；
- **Comet** ：长轮询 和 流；
- **服务器发送事件** ：SSE ；

#### **WebSocket**

用于在一个单独的持久连接上提供 **全双工、双向通信** 。

**原生写法**

- ```js
  var socket = new WebSocket(url);
  socket.send(data);
  socket.onmessage = function(event) {
    var data = event.data;
  };
  // 其他3个事件
  socket.onopen = function(){};
  socket.onerror = function(){};
  socket.onclose = function(){};
  
  ```

**说明**

- **url** ：传输协议为 `ws://` 和 `wss://` ，分别对应 `未加密` 和 `加密` 的协议；
- **send** ：`data` 只能是 `字符串` ;
- **onmessage** ：`服务器` 返回的数据存放在 `event.data` 中；

**优缺点**

**优点** ：

- 能够双向通信，`服务器` 能主动推送数据给 `浏览器` ；
- 传输数据小，占用资源少，速度快。因为相比较 `http/https` 的传输协议，`ws/wss` 的请求头更小，所以每次传输数据，“无效”的请求头数据会更少，对于高频率的请求效果更明显；

**缺点** ：

- **h5** 新增的功能，对于某些旧浏览器并不支持；
- 需要使用不同的 web服务器(传输协议为 `ws/wss` ) ；

### 高级技巧

#### 高级函数

**安全的类型检测**

- `instanceof` 检测时，必须是在同一个全局作用域下才有效(比如一个页面包含多个frame时，在 非构造函数页面 检测会返回false)；

- 利用 `原生XX` 的构造函数名与全局作用域无关，因此使用 `toString()` 就能够保证返回一致的值，所以可以创建以下函数；

  ```js
  // 检测数组
  function isArray(value) {
    return Object.prototype.toString.call(value) == "[object Array]";
  }
  // 检测函数
  function isFunction(value) {
    return Object.prototype.toString.call(value) == "[object Function]";
  }
  // 检测正则表达式
  function isRegExp(value) {
    return Object.prototype.toString.call(value) == "[object RegExp]";
  }
  ```

**作用域安全的构造函数**

- 当使用构造函数创建对象时，忘记使用 `new` 的情况下；

  ```js
  function Person(name, age, job) {
    if (this instanceof Person) {
      this.name = name;
      this.age = age;
      this.job = job
    } else {
      return new Person(name, age, job);
    }
  }
  ```

- 原理：使用 `new` 的情况下，`this` 指向的是 `new` 出来的新对象；未使用 `new` 的情况下，`this` 指向的是 `全局对象window` ；

**惰性载入函数**

- 在某些 `if` 判断的语句中，**如果第一次怎样，后面每次都会这样**，针对这种情况，可以使用 `惰性载入函数` ，即 **if里重写原方法**，这样后面执行是就不用判断了；

- `惰性载入函数` 有两种写法：

  - 第一种是：函数被调用时再处理函数：

  - 第二种是：声明函数时就指定适当的函数：

    ```js
    // 原函数
    function createXHR() {
      if (typeof XMLHttpRequest != "undefined") {
        return new XMLHttpRequest();
      } else if (typeof ActiveXObject != "undefined") {
        //...
        return new ActiveXObject(arguments.callee.activeXString);
      } else {
        throw new Error("No XHR object available.");
      }
    }
    ```

    ```js
    // 第一种：函数被调用时再处理函数
    function createXHR() {
      if (typeof XMLHttpRequest != "undefined") {
        createXHR = function(){		// 改变1
          return new XMLHttpRequest();
        }
      } else if (typeof ActiveXObject != "undefined") {
        createXHR = function() {		// 改变2
          //...
          return new ActiveXObject(arguments.callee.activeXString);
        }
      } else {
        createXHR = function() {		// 改变3
          throw new Error("No XHR object available.")
        }
      }
    }
    ```

    ```js
    // 第二种：声明函数时就指定适当的函数
    var createXHR = (function() {
      if (typeof XMLHttpRequest != "undefined") {
        return function() {		// 改变1
          return new XMLHttpRequest();
        }
      } else if (typeof ActiveXObject != "undefined") {
        return function() {		// 改变2
          // ...
          return new ActiveXObject(arguments.callee.activeXString);
        }
      } else {
        return function() {		// 改变3
          throw new Error("No XHR object available.")
        }
      }
    })();		// 改变4
    ```

**函数绑定**

- `函数绑定` 要创建一个函数，可以在特定的 `this` 环境中以指定参数调用另一个函数；

- 来看下面这个例子：

  ```js
  var handler = {
    message: "Event handler",
    handlerClick: function() {
      alert(this.message);
    }
  };
  
  var btn = document.getElementById("my-btn");
  // 返回undefined
  EventUtil.addHandler(btn, "click", handler.handlerClick);
  // 返回Event handler, 
  // 传入作为this值的对象，和 call，apply 类似
  EventUtil.addHandler(btn, "click", handler.handlerClick.bind(handler));
  ```

**函数柯里化**

- 用于创建已经设置好了一个或多个参数的函数；

- `函数柯里化` 的基本方法和 `函数绑定` 是一样的，都是使用一个闭包返回一个函数；

- 两者的区别在于：当函数被调用时，返回的函数还需要设置一些传入的参数；

- 来看下面的例子：

  ```js
  // 函数柯里化 定义函数
  function curry(fn) {
    var args = Array.prototype.slice.call(arguments, 1);
    return function() {
      var innerArgs = Array.prototype.slice.call(arguments);
      var finalArgs = args.concat(innerArgs);
      return fn.apply(null, finalArgs);
    }
  }
  ```

  ```js
  // 函数柯里化 使用方法1
  // 超出参数自动忽略
  function add(num1, num2) {
    return num1 + num2;
  }
  var curriedAdd = curry(add, 5);
  alert(curriedAdd(3));		// 8
  ```

  ```js
  // 函数柯里化 使用方法2
  // 超出参数自动忽略
  function add(num1, num2) {
    return num1 + num2;
  }
  var curriedAdd = curry(add, 5, 13, 2);
  alert(curriedAdd());		// 18
  ```

#### 防篡改对象

**注意：一旦把对象定义为防篡改，就不可撤销了** 。

**不可扩展对象**

- 不能添加属性和方法，可删除和修改；
- 使用 `Object.preventExtensions(obj)` 把 `obj对象` 修改为 `不可扩展对象` ；

**密封的对象**

- 不能添加和删除属性和方法，但可修改；
- 使用 `Object.seal(obj)` 把 `obj对象` 修改为 `密封的对象` ；

**冻结的对象**

- 既不可扩展，又是冻结的。即不可新增、修改、删除属性和方法；
- 使用 `Object.freeze(obj)` 把 `obj对象` 修改为 `冻结的对象` ；

#### 高级定时器

`setTimeout` 和 `setInterval()` 并不是 **过xx毫秒后执行函数** ，而是 **过xx毫秒后把函数代码添加到代码队列，在浏览器空闲时，将按队列顺序依次执行队列中的代码** 。

**重复的定时器**

- 使用 `setInterval()` 时，仅当没有该定时器的任何其他代码实例时，才将定时器代码添加到队列中，这确保了队列中的最小时间间隔为指定间隔；

- 当这种 `重复定时器` 有两个问题：(1)某些间隔会被跳过；(2)多个定时器的代码执行之间的间隔可能会比预期的小；

- 为了解决这种问题，可以使用如下模式的 `链式setTimeout()调用` ；

  ```js
  setTimeout(function(){
    // 处理中
    setTimeout(arguments.callee, interval);
  }, interval);
  ```

**Yielding Processos**

- 当某个循环占用了大量时间，如果该循环满足 **1无需同步完成，2无需按顺序完成** ，则可使用一种叫做 `数组分块` 的技术，小块小块的处理数组，通常每次一小块；

- 基本思路：为要处理的数组创建一个队列，然后使用定时器取出下一个要处理的项目进行处理，接着再设置另一个定时器；

- 看下面这个例子：

  ```js
  // 思路
  setTimeout(function(){
    // 取出下一个项目并处理
    var item = array.shift();
    process(item);
    
    //若还有条目，再设置另一个定时器
    if (array.length > 0) {
      setTimeout(arguments.callee, 100);
    }
  }, 100)
  ```

  ```js
  // 实际例子
  function chunk(array, process, context) {
    setTimeout(function() {
      var item = array.shift();
      process.call(context, item);
      
      if (array.length > 0) {
        setTimeout(arguments.callee, 100);
      }
    }, 100);
  }
  
  var data = [12,123,23,234,1234,323,444,555,666,777];
  function printValue(item) {
    var div = document.getElementById("myDiv");
    div.innerHTML += item + "<br>";
  }
  
  chunk(data, printValue);
  ```

**函数节流**

- 浏览器中某些操作(比如DOM)非常耗资源，高频率的进行这种操作可能会让浏览器崩溃。为了解决这个问题，可以使用定时器对该函数进行 **节流** ；

- `函数节流` 背后的思路是：某些代码不可以在没有间断的情况下连续重复执行。第一次调用函数时，会创建一个定时器，在指定的时间间隔后再执行函数；第二次调用时，会先清除前一次的定时器，并设置另外一个，然后重复前面的步骤；

- 来看下面的例子：

  ```js
  // 思路
  function throttle(method, context) {
    clearTimeout(method.tId);
    var method.tId = setTimeout(function() {
      method.call(context);
    }, 100)
  }
  ```

  ```js
  // 未节流前写法，高频率的更新可能导致浏览器崩溃
  window.onresize = function() {
    var div = document.getElementById("myDiv");
    div.style.height = div.offsetWidth + "px";
  };
  ```

  ```js
  // 节流写法
  function resizeDiv() {
    var div = document.getElementById("myDiv");
    div.style.height = div.offsetWidth + "px";
  }
  
  window.onresize = function() {
    throttle(resizeDiv);
  }
  ```

#### 自定义事件

- `事件` 是一种叫做 `观察者` 的设计模式，这是一种创建松散耦合代码的技术；
- `观察者模式` 由 `主体` 和 `观察者` 两部分组成；
  - **主体** ：发布事件；
  - **观察者** ：订阅事件，观察主体； 

### Cookie

- `服务器` 生成并返回给 `浏览器` 的；
- 绑定在特定域名下，只在绑定的域名下有效；
- (针对域名) 只要有的话，浏览器每次发数据给 `服务器` ，一定会带上已经存在的 `cookie ；`
- 未设定有效期的 `cookie` ，浏览器关闭后即失效，设定有效期的 `cookie`，手动删除或到期前，一直有效；

### 最佳实践

**优化DOM**

在 **JavaScript** 各个方面中，**DOM** 毫无疑问是最慢的一部分。

当往页面添加多个 `<li>` 元素时，每添加一次都要进行一次 `现场更新`，即页面要进行一次 `重排重绘` ，性能损失非常大。

**最小化更新现场**

- 使用 `文档片段` 来构建DOM结构，接着将其添加到 `List` 元素中，这样只会进行一次 `现场更新` ；

  ```js
  var list = document.getElementById("myList"),
      fragment = document.createDocumentFragment(),
      item,
      i;
  for (i=0;i<10;i++) {
    item = document.createElement("li");
    fragment.appendChild(item);
    item.appendChild(document.createTextNode("Item" + i));
  }
  list.appendChild(fragment);
  ```

**使用 innerHTML**

- 创建 **DOM** 节点到另一种方法(前一种是 `createElement` 、`appendChild` 之类的)；

- 对于小的 **DOM** 节点更改，两种方法效率差不多，然而对于大的 **DOM** 节点的更改，`innerHTML` 要比前一种快的多，因为只进行一次 `现场更新` ；

  ```js
  var list = document.getElementById("myList"),
      html = "",
      i;
  for (i=0;i<10;i++) {
    html += "<li>Item" + i + "</li>";
  }
  list.innerHTML = html;
  ```

**使用事件代理**

- 利用 `事件冒泡` ，在文档级别附加事件处理程序，这样可以处理整个页面的事件；

**注意 HTMLCollection**

- 任何时候要访问 **HTMLCollection** ，不管它是一个属性还是一个方法，都是在文档上进行一个查询，这个查询开销很大；

- 最小化访问 **HTMLCollection** 的次数可以极大的改进脚本的性能；

- 优化 **HTMLCollection** 最重要的地方就是循环了；

- 以下情况会返回 **HTMLCollection** 集合：

  - 进行了对 `getElementsByTagName` 的调用 ；
  - 获取了元素的 `ChildNodes` 属性 ；
  - 获取了元素的 `attributes` 属性 ；
  - 访问了特殊的集合，比如 `document.forms`、`document.images` 等 ；

- 来看下面的例子：

  ```js
  // 优化前
  var images = document.getElementsByTagName("img"),
      i,
      len;
  for(i=0, len=images.length;i<len;i++) {
    //...
  }
  
  // 优化后
  var images = document.getElementsByTagName("img"),
      image,
      i,
      len;
  for(i=0, len=images.length;i<len;i++) {
    image = images[i];
    //...
  }
  ```

**部署**

**压缩** ：

压缩分两部分，即 `文件压缩` 和 `HTTP压缩` 。

`文件压缩` ：

- 把开发环境的文件( js、css 等)压缩，然后部署到服务器上；
- 删除额外的空白，包括换行；
- 删除所有注释；
- 缩短变量名；

`HTTP压缩` ：

- 服务器对传输给浏览器的代码进行压缩(服务器要进行配置)；
- 浏览器对接收的资源进行解压缩，目前五大 Web浏览器都支持解压缩；；
- 可以节省传输的字节，减少带宽消耗，加快响应速度；
- **原理** ：一个指定了文件 使用了给定格式 进行了压缩的HTTP头 包含在了服务器响应中。接着浏览器会查看该 HTTP头确定文件是否已被压缩，然后使用合适的格式进行解压缩；

### 新API

**requestAnimationFrame()** 

- 早期动画循环；

**Page Visiblity API**

- `document.hidden` ：查看页面是否隐藏的布尔值。页面隐藏包括在后台标签页中或最小化浏览器；
- `document.visibilityState` ：表示下列4个可能状态的值：
  - 页面在后台标签中或浏览器最小化；
  - 页面在前台标签页中；
  - 实际的标签页已经隐藏，但用户可以看到页面的预览(比如win7鼠标移到任务栏的图标上看到的预览)；
  - 页面在屏幕外执行预渲染处理；
- `visibilitychange` 事件：文档从可见变为不可见，或从不可见变为可见时触发；

**Geolocation API**

- 地理定位 API；
- `navigator.geolocation` 对象；
- `getCurrentPosition` ：获取当前位置；
- `watchPosition` ：跟踪用户位置，与定时调用 `getCurrentPosition` 效果一样；
- `clearWatch` ：取消跟踪用户位置；

**File API**

- 能够在浏览器端读取本地文件的信息(文件名称，类型，大小，修改时间，文件内容等)；
- 使用 `<input type="file">` 元素，返回对象的 `files` 属性；
- **FileReader 类型**
  - `var reader = new FileReader()` ；
  - `readAsText(file, encoding)` ：以纯文本形式读取文件，数据保存在 `result` 属性中；
  - `readAsDataURL(file)` ：读取文件并将文件以 URI 的形式保存在 `result` 属性中； 
- **对象 URL**
  - 引用保存在 `File` 或 `Blob` 中数据的 URL；
  - `window.URL.createObjectURL(file)` ：获取 `file` 的URL；

**Web 计时**

- `window.performance` ：获取页面的所有度量信息(即时间相关信息)；

**Web Workers**

- **JavaScript** 是单线程的；

- 让 **JavaScript** 在后台运行，类似开了一个后台线程；

- 来看下面的例子：

  ```js
  var worker = new Worker("stufftodo.js");
  // 这行代码会让浏览器下载js，但只有 worker接收到消息才会执行js代码
  // 使用 postMessage() 给 workder发送消息，可以是字符串或对象
  worker.postMessage("start! ");
  // worker返回到数据保存在 event.data 中
  worker.onmessage = function(event) {
    var data = event.data;
    //...
  };
  worker.onerror = function(event) {
    
  };
  // 立即停止 workder 到工作
  worker.terminate();
  ```

## 跳过的章节

其中有几章没看跳过了，有些是已经熟悉的，有些是优先级不高暂时跳过了，以后有时间了再补上吧～

跳过的章节有：

- 第8章：BOM；
- 第9章：客户端检测；
- 第10～12章：DOM；
- 第13章：事件；
- 第14章：表单；
- 第15章：Canvas；
- 第18章：JS与XML；
- 第19章：E4X；

## 严格模式

有关 `严格模式` 与 `非严格模式` 的区别，请看下篇文章。

[点我查看](/2019/06/13/strict-mode)
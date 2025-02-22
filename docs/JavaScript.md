## 19.7.24

### 浏览器渲染流程

1.处理 HTML 并构建 DOM 树  
2.处理 CSS 构建 CSSOM 树  
3.将 DOM 与 CSSOM 合并成一个渲染树  
4.根据渲染树来布局,计算每个节点的位置  
5.调用 GPU 绘制,合并图层,显示在屏幕上

### 阻塞渲染

在构建 CSSOM 树时，会阻塞渲染，直到 CSSOM 树构建完毕。  
当 HTML 解析到 script 标签时，会暂停构建 DOM，直到解析完后才会在暂停的地方重新开始。  
并且 CSS 也会影响 JS 的执行，只有解析完样式表才会去执行 JS，所以也可以认为这种情况下，CSS 也会暂停构建 DOM。

head 内的 js 脚本会阻塞 body 标签的渲染，把 js 脚本放在 head 内需要等到 js 执行完毕后才会渲染 body。  
而将样式文件写在 head 中是为了避免页面元素由于样式缺失造成的白屏现象。

https://yuchengkai.cn/docs/frontend/browser.html#%E6%B8%B2%E6%9F%93%E6%9C%BA%E5%88%B6

### link标签下的css文件和js文件是同步/异步进行下载/执行的
- css文件: 同步下载、同步执行，所以会阻塞渲染
- js文件: 默认情况下同步下载、同步执行，也会阻塞渲染
- js异步下载：
  - async: 异步下载后立即执行
  - defer: 异步下载后等html解析完成再执行

## 19.7.26

### 浏览器事件循环

JS 中有一个主线程和调用栈，所有的任务都会放入到调用栈里面等待主线程执行。  
调用栈：后进先出，当函数被调用时，会被添加到栈中的顶部，执行完毕后从顶部移除这个函数，直到调用栈被清空。  
任务队列：先进先出，如果是同步函数则会在调用栈中按顺序被主线程调用，如果是异步函数则会在异步有了结果之后，将注册的回调函数放入到任务队列中，等到调用栈被清空了，也就是主线程空闲的时候，被读取到调用栈中等待执行。

宏任务：包括整体代码 script，setTimeout,setInterval 等  
微任务：Promise，process.nextTick 等  
进入整体代码（宏任务）后，开始第一次循环，执行所有的微任务。接着再次从宏任务开始，找到其中一个宏任务队列执行完毕，再执行所有微任务。以此循环。

**调用栈中的同步任务都执行完毕了，栈内就清空了，代表主线程空闲了，就会去任务队列按顺序读取一个任务放入栈中执行。每次栈内被清空，就会去读取任务队列还有没有任务，有就读取执行，一直循环读取-执行的操作，这就形成了事件循环。**

**对于 setInterval(fn,ms)来说，不是每过 ms 秒会执行一次 fn，而是每过 ms 秒，会有 fn 进入 Event Queue。一旦 setInterval 的回调函数 fn 执行时间超过了延迟时间 ms，那么就完全看不出来有时间间隔了。**

![事件循环](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2017/11/21/15fdcea13361a1ec~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)
https://github.com/aooy/blog/issues/5  
https://juejin.im/post/59e85eebf265da430d571f89  
https://www.cnblogs.com/yqx0605xi/p/9267827.html

**async/await**

- async：只是一个语法糖，只是帮助我们返回一个 Promise 而已
- await：使用 await 时，会从右往左执行，当遇到 await 时，会阻塞函数内部处于它后面的代码，去执行该函数外部的`同步代码`，当外部同步代码执行完毕，再回到该函数内部执行剩余的代码, 并且当 await 执行完毕之后，会先处理微任务队列的代码(可以理解为会将 await 后的内容塞到微任务队列中，若遇到 await 则一次只会塞入一个 await)

```javascript
console.log(1);

async function fn() {
  console.log(2);
  await console.log(3);
  console.log(4);
}
fn();

new Promise((resolve) => {
  console.log(5);
  resolve();
}).then((res) => console.log(6));

setTimeout(() => console.log(7), 0);

console.log(8);

// 1 2 3 5 8 4 6 7
```

```javascript
console.log(1);

async function fn() {
  console.log(2);
  await console.log(3);
  await console.log(4);
  await console.log(11);
  await console.log(22);
  await console.log(33);
  await console.log(44);
}
setTimeout(() => {
  console.log(5);
}, 0);
fn();

new Promise((resolve) => {
  console.log(6);
  resolve();
}).then(() => {
  console.log(7);
});

console.log(8);

//  1 2 3 6 8 4 7 11 22 33 44 5
```

https://www.cnblogs.com/smile-fanyin/p/14622432.html

```javascript
console.log(1);

async function fn() {
  console.log(2);
  await console.log(3);
  await console.log(4);
  console.log(5);
}
fn();

new Promise((res, rej) => {
  console.log(6);
  res(7);
  console.log(8);
  rej(9);
}).then((n) => console.log(n))
.catch((e) => console.log(e));

setTimeout(() => console.log(10), 0);

console.log(11);

//  1 2 3 6 8 11 4 7 5 10
```
## 19.7.29

### 图层

一般来说，可以把普通文本流看成一个图层，特定的属性可以生成一个新的图层。不同的图层渲染互不影响，所以对于某些频繁需要渲染的建议单独生成一个新图层，提高性能。但也不能生成过多的图层，会引起反作用。  
通过以下几个属性可以生成新图层

- 3D 变换：translate3d、translateZ
- will-change
- video、iframe 标签
- 通过动画实现的 opacity 动画转换
- position:fixed

### 重绘和回流

重绘是当前节点需要更改外观而不会影响布局的，比如改变 color 就称为重绘  
回流是布局或者几何属性需要改变就称为回流  
**回流一定会发生重绘，重绘不一定会引起回流。回流所需的成本比重绘高的多，改变深层次的节点可能会导致父节点的一系列回流。**  
**减少重绘和回流**  
1.用 translate 替代 top  
2.用 visibility 替代 display：none，前者只会引起重绘，后者改变布局会引起回流  
3.先把 DOM 给隐藏了(display：none)了，接着对 DOM 进行若干修改后，再将 DOM 显示出来  
4.不要使用 table 布局(tr,td)，可能很小的一个改动会引起整个 table 的重新布局  
5.将频繁运行的动画变为图层，图层能够阻止该节点回流影响到别的元素

https://yuchengkai.cn/docs/frontend/browser.html#%E5%9B%BE%E5%B1%82

## 19.7.30

### 跨域

浏览器出于安全考虑，有同源策略。也就是说域名、端口、协议有一个不同就是跨域。（IE 无视端口规则，即同域名下不同端口间可以正常请求）  
**如何解决跨域**

- JSONP：利用<script>标签没有跨域限制的漏洞。通过<script>标签指向一个需要访问的地址并提供一个回调函数来接收数据。  
  JSONP 只限于 get 请求
- CORS:服务端设置 Access-Control-Allow-Origin 就可以开启 CORS。该属性表示哪些域名可以访问资源，如果设置通配符则表示所有网站都可以访问资源。
- document.domain:该方法只能应用于二级域名相同的情况下，比如 id.qq.com 和 game.qq.com 适用该方法。  
  只需要给页面添加 document.domain = 'qq.com'表示二级域名都相同就可以实现跨域。
- postMessage:常用于获取嵌入页面中的第三方页面数据，一个页面发送消息，另一个页面判断来源，并接收消息。

https://yuchengkai.cn/docs/frontend/browser.html#%E8%B7%A8%E5%9F%9F

## 19.8.1

### new 的过程

1.新生成一个对象  
2.链接到原型  
3.绑定 this  
4.返回新对象

- 以构造器的 prototype 属性为原型，创建新对象；
- 将 this(也就是上一句中的新对象)和调用参数传给构造器，执行；
- 如果构造器没有手动返回对象，则返回第一步创建的新对象，如果有，则舍弃掉第一步创建的新对象，返回手动 return 的对象。

```javascript
// 构造器函数
let Parent = function (name, age) {
  this.name = name;
  this.age = age;
};
Parent.prototype.sayName = function () {
  console.log(this.name);
};
//自己定义的new方法
let newMethod = function (Parent, ...rest) {
  // 1.以构造器的prototype属性为原型，创建新对象；
  let child = Object.create(Parent.prototype);
  // 2.将this和调用参数传给构造器执行
  let result = Parent.apply(child, rest);
  // 3.如果构造器没有手动返回对象，则返回第一步的对象
  return typeof result === "object" ? result : child;
};
//创建实例，将构造函数Parent与形参作为参数传入
const child = newMethod(Parent, "echo", 26);
child.sayName(); //'echo';

//最后检验，与使用new的效果相同
child instanceof Parent; //true
child.hasOwnProperty("name"); //true
child.hasOwnProperty("age"); //true
child.hasOwnProperty("sayName"); //false
```

### 原型链

![原型链](https://camo.githubusercontent.com/8c32afe801835586c6ee59ef570fe2b322eadd6e/68747470733a2f2f79636b2d313235343236333432322e636f732e61702d7368616e676861692e6d7971636c6f75642e636f6d2f626c6f672f323031392d30362d30312d3033333932352e706e67)

```javascript
// function(){} 为构造函数
const fn = function () {};
//  prototype 指向原型（一个对象） {constructor: ƒ}
fn.prototype;
//  constructor  指向原型的构造函数
fn.prototype.constructor === fn;
//  __proto__  指向创建该对象的构造函数的原型  即Function.prototype
fn.__proto__ === Function.prototype;
//  访问创建fn的构造函数 即Function(){}
fn.__proto__.constructor === Function;

// 创建一个对象
const obj = { a: 1 };
// 对象由Object(){}创建
obj.constructor === Object;
//  对象没有prototype属性
obj.prototype === undefined;
// __proto__ 指向创建该对象的构造函数的原型 即Object.prototype
obj.__proto__ === Object.prototype;
```

每一个函数都有`prototype`属性，该属性指向原型。除了 Function.prototype.bind()，通过 bind 方法生成的函数没有`prototype`属性。  
每一个对象都有`__proto__`属性，指向创建该对象的构造函数的原型。  
`Function.prototype`和`Object.prototype`是两个特殊的对象，他们由引擎来创建。  
函数的`prototype`是一个对象，也就是原型。  
对象的`__proto__`指向原型，`__proto__`将对象和原型连接起来组成了原型链。

#### Function.proto === Function.prototype

所有对象都可以通过原型链最终找到`Object.prototype`，虽然`Object.prototype`也是一个对象，但是这个对象不是`Object`创建的，而是引擎自己创建的`Object.prototype`。  
**所以可以这么说，所有实例都是对象，但是对象不一定都是实例。**  
`Function.prototype`这个对象其实是个函数，这个函数也是引擎自己创建的。  
首先引擎创建了`Object.prototype`，接着创建`Function.prototype`,并且用`__proto__`将两者连接起来。  
**所以得出结论，不是所有函数都是`new Function()`产生的。**  
有了`Function.prototype`后才有了`function Function(){}`，然后其他的构造函数都是`Function()`生成的。  
由于其他构造函数都可以通过原型链找到`Function.prototype`，并且`function Function()`本质也是函数，为了不产生混乱就将`function Function()`的`__proto__`联系到`Function.prototype`上。

```javascript
function foo() {
  //  设置私有属性 此时将foo视为普通对象 通过foo.a()访问
  foo.a = function () {
    console.log(1);
  };
  this.a = function () {
    console.log(2);
  };
}
//  通过prototype绑定的属性为公有属性 此时可将foo视为class 可通实例.a()进行访问
foo.prototype.a = function () {
  console.log(3);
};
Function.prototype.a = function () {
  console.log(4);
};

//  此时调用静态方法
foo.a(); //  此时未实例化 函数也没执行 foo.a是在函数体内执行 此时找不到foo.a只能去原型链找
const obj = new foo(); //  建立原型链
//  此时有两个a方法 一个内部方法 一个外部公有方法 优先调用内部方法
obj.a();
//  此时foo函数内部属性已初始化 函数内部的静态方法覆盖原静态方法
foo.a();

//  4 2 1
```

#### 箭头函数没有原型链
```javascript
const A = () => ({})
console.log(new A())  // Uncaught TypeError: A is not a constructor

const B = function() { return {}}
console.log(new B())  //  {}
```
https://github.com/KieSun/Dream/issues/2

## 19.8.5

### 安全

#### XSS

XSS 通过修改 HTML 节点或者执行 JS 代码来攻击网站。  
通常的防御手段是转义输入的内容，对引号、尖括号、斜杠进行转义。  
例如通过 URL 获取某些参数

```html
<!-- http://www.domain.com?name=<script>alert(1)</script> -->
<div>{{name}}</div>
```

#### CSRF

CSRF 就是利用用户的登录状态发起恶意请求。  
如果是 Get 请求则可以在 img 标签中设置图片地址为对应接口，如果是 Post 请求则需要用表单来提交接口。  
**如何防御**  
1.Get 请求不对数据进行修改。  
2.Cookie 设置`SameSite`属性，使 Cookie 不随着跨域请求发送。  
3.阻止第三方网站请求接口。  
4.请求时附带验证信息，如 token。  
5.验证 Referer。浏览器发送请求时会带上 Referer，通过验证 Referer 判断请求是否是第三方网站发起的。

#### CSP

CSP 本质上是建立白名单，规定浏览器只能执行特定来源的代码。  
通常可以在 HTTP Header（请求头）或者 HTML 的 meta 标签中设置`Content-Security-Policy`（只允许加载本站资源/只加载 HTTPS 协议图片/允许加载任何来源框架）来开启 CSP。

https://yuchengkai.cn/docs/frontend/safety.html#xss

### H5 新特性

1.video/radio  
2.canvas  
3.webSocket  
4.webWorker js 多线程  
5.语义化标签如 header、footer、nav 等  
6.新增了很多表单属性如 min 和 max、autofocus、placehoder 等  
7.sessionStorage 短期存储浏览器关闭就删除;localStorage 长期数据存储，与 cookie 相比 cookie 大小只有 4kb 左右，而 localStorage 有 5Mb。

### 继承

使用 call 或 apply 借用其他构造函数的成员。

```javascript
//  父类
function Person(name) {
  this.name = name;
  this.attr = ["小黄", "小白"];
  this.print = () => {
    console.log(this.name);
  };
}
//  子类
function Student(name) {
  Person.call(this, name);
}

const a = new Person("A");
a.print(); // A
const b = new Student("B");
b.print(); // B
b.attr.push("小黑");
console.log(b.attr); // ["小黄", "小白", "小黑"]
console.log(a.attr); // ["小黄", "小白"]
```

https://www.jianshu.com/p/b76ddb68df0e

### 深拷贝浅拷贝

```javascript
//  浅拷贝
const a = { a: 1, b: 2, c: 3 };
const b = a;
b.d = 4;
console.log(b); //  {a: 1, b: 2, c: 3, d: 4}
console.log(a); //  {a: 1, b: 2, c: 3, d: 4}

//  深拷贝
function clone(num) {
  let newNum;
  if (num instanceof Array) {
    newNum = [];
    num.map((e, index) => (newNum[index] = clone(num[index])));
    return newNum;
  } else if (num instanceof Object) {
    newNum = {};
    for (let i in num) {
      newNum[i] = clone(num[i]);
    }
    return newNum;
  } else {
    return num;
  }
}
const c = clone(a);
c.e = 5;
console.log(c); //  {a: 1, b: 2, c: 3, d: 4, e: 5}
console.log(a); //  {a: 1, b: 2, c: 3, d: 4}
```

### super()

说明：`super`是 es6 新增的语法糖 用于访问父类。  
功能：在构造函数中调用`super`相当于把父类的`construcrtor`给执行了，并且将`this`指向指定为子类。`super`中传递的参数相当于给父类的`constructor`传递参数。  
注意事项：如果定义了`class`但是没有写`construcrtor`方法，那么编译器会自动加入`construcrtor`，并且在其中调用`super`方法。如果使用`extends`继承父类后写了`construcrtor`方法但是没有调用`super`，则子类拿不到`this`对象，并且会抛出异常。  
https://es6.ruanyifeng.com/#docs/class-extends  
https://www.jianshu.com/p/2a5a7352f4e5

### 图片懒加载实现原理

可视区域：`document.documentElement.clientHeight`  
滚动距离：`document.documentElement.scrollTop`  
元素距离页面顶部的距离：`e.offsetTop`  
判断元素加载条件：**可视区域 + 滚动距离 > 元素距离页面顶部距离**  
[[具体实现](https://github.com/Elderkly/Lazyload/blob/master/index.html)]  
![Lazyload](https://picb.zhimg.com/80/v2-af1ab0c5f34e468e8647135c1f9f51e4_720w.jpg)  
https://zhuanlan.zhihu.com/p/55311726

### 正则

```javascript
/**
    pattern：正则表达式
    flags:标识(修饰符)
        标识主要包括：
        1. i 忽略大小写匹配
        2. m 多行匹配，即在到达一行文本末尾时还会继续寻常下一行中是否与正则匹配的项
        3. g 全局匹配 模式应用于所有字符串，而非在找到第一个匹配项时停止
*/
const reg = /pattern/flags                  //  字面量创建
const reg2 = new RegExp(pattren, flags)     //  实例创建 可进行字符串拼接
```

正则截取`id`后的内容  
`'id:123123'.match(/id(\W*)/)[1]`或`new RegExp('id(\\S*)').exec('id:123123')[1]`

## 常用数组操作 API

| 序号 |      API      | 描述                                                                                                                                                                     |     返回值     | 是否改变原数组 |
| :--: | :-----------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------: | :------------: |
|  1   |    push()     | 在最后插入一个或多个数据                                                                                                                                                 |    数组长度    |      改变      |
|  2   |   unshift()   | 在头部插入一个或多个数据                                                                                                                                                 |    数组长度    |      改变      |
|  3   |     pop()     | 弹出最后一个数据                                                                                                                                                         |   删除的数据   |      改变      |
|  4   |    shift()    | 弹出第一个数据                                                                                                                                                           |   删除的数据   |      改变      |
|  5   |   reverse()   | 逆置数据                                                                                                                                                                 |      数组      |      改变      |
|  6   |    join()     | 将数组转为字符串                                                                                                                                                         |     字符串     |     不改变     |
|  7   |    slice()    | 截取指定位置的数组                                                                                                                                                       |    截取内容    |     不改变     |
|  8   |   concat()    | 合并数组                                                                                                                                                                 |    合并内容    |     不改变     |
|  9   |    sort()     | 排序                                                                                                                                                                     |    排序结果    |      改变      |
|  10  |   splice()    | 删除指定位置，并替换                                                                                                                                                     |  删除后的数组  |      改变      |
|  11  | lastIndexOf() | 反向查询数据的索引                                                                                                                                                       |      索引      |     不改变     |
|  12  |   filter()    | 筛选符合回调函数的数据                                                                                                                                                   |      数组      |     不改变     |
|  13  |    every()    | 判断数组元素是否符合回调函数条件，全部元素都满足则返回 true                                                                                                              |    boolean     |     不改变     |
|  14  |    some()     | 对标 every(),只要有一个元素符合条件则返回 true                                                                                                                           |    boolean     |     不改变     |
|  15  |   reduce()    | reduce() 可同时将前面数组项遍历产生的结果与当前遍历项进行运算,接收两个参数，第一个为回调函数，第二个为初值，若指定了初值则从第一个元素开始遍历，否则从第二个元素开始遍历 | 自定义返回类型 |     不改变     |
|  16  | reduceRight() | 同 reduce()不过从右往左遍历                                                                                                                                              | 自定义返回类型 |     不改变     |

https://blog.csdn.net/BBBBobo/article/details/121869585  
https://blog.csdn.net/qq_38970408/article/details/121018660

## 截取一个数字的百位、十位、个位

- 力扣 1281  
  `Array.from(String(n), Number)`

**https://blog.csdn.net/yangaoyuan1999/article/details/119993661**

## Set、Map、WeakSet 和 WeakMap 的区别？

|    Api    | 特点                                                                                     | 属性               | 方法                                                                           |
| :-------: | :--------------------------------------------------------------------------------------- | :----------------- | :----------------------------------------------------------------------------- |
| Set(集合) | 成员唯一、有序不重复、可遍历，类似`Array`                                                | `size`类似`length` | `add`、`delete`、`has`、`clear`、`keys`、`values`、`entries`、`forEach`        |
|  WeakSet  | 只能存放对象引用，不能存放值，不可遍历，存放的对象为弱引用，即不计入引用计数，会被回收掉 |                    | 同 Set 但没有遍历的 Api                                                        |
| Map(字典) | 类似`Set`但以`[key,value]`来存储，有序不重复、可遍历                                     | `size`             | `get`、`set`、`has`、`delete`、`clear`、`keys`、`values`、`entries`、`forEach` |
|  WeakMap  | 类似`WeakSet`但只接受对象作为键名，有序不重复、不可遍历                                  |                    | 同 Map 但没有遍历的 Api                                                        |

**https://github.com/sisterAn/blog/issues/24**

## 上传图片

传了 formData 就不用制定 Content-Type 了。  
**https://zhuanlan.zhihu.com/p/34291688**

## sort

> 如果 compareFunction(a, b) 小于 0 ，那么 a 会被排列到 b 之前；  
> 如果 compareFunction(a, b) 等于 0 ， a 和 b 的相对位置不变。备注： ECMAScript 标准并不保证这一行为，而且也不是所有浏览器都会遵守（例如 Mozilla 在 2003 年之前的版本）；  
> 如果 compareFunction(a, b) 大于 0 ， b 会被排列到 a 之前。  
> compareFunction(a, b) 必须总是对相同的输入返回相同的比较结果，否则排序的结果将是不确定的。

**!!!!!sort 若不传参数则默认按首位进行排序，若想得到升序最好传入函数!!!!!!!**

```JavaScript
// eg.(1)
[-1,-2,-3,1,2,4].sort() // [-1,-2,-3,1,2,4]
[-1,-2,-3,1,2,4].sort((a,b) => a - b)  //  [-3,-2,-1,1,2,4]

//  eg.(2)
[10,1,2,7,6,1,5].sort() // [1,1,10,2,5,6,7]
[10,1,2,7,6,1,5].sort((a,b) => a - b) // [1,1,2,5,6,7,10]
```

**注意 a 一般指向数组后一项并不是前一项**
**https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/sort**

## 创建二维数组

```javascript
Array.from(new Array(length), () => new Array(length).fill(false));
```

## react 图片上传

```javascript
<div
  className="border-2 h-12 border-dotted cursor-pointer flex justify-center items-center text-white border-[#8692AF] text-base"
  onDragEnter={onDragEnter}
  onDragLeave={onDragLeave}
  onDragOver={onDragOver}
  onDrop={onDrop}
></div>;

const onDragEnter = (e: any) => {
  console.log("onDragEnter");
  e.preventDefault();
};

const onDragOver = (e: any) => e.preventDefault();

const onDragLeave = (e: any) => {
  console.log("onDragLeave");
  e.preventDefault();
};
const onDrop = (e: any) => {
  e.preventDefault();
  console.log(e.dataTransfer?.files?.[0]);
};
```

## 性能优化

**https://juejin.cn/post/6949896020788690958**

## Date

```JavaScript
//  获取某一天的23.59.59
new Date(time).setHours(23,59,59)
```

## Array.from

对一个类似数组或可迭代对象创建一个新的，浅拷贝的数组实例。

```javascript
console.log(Array.from("foo"));
// expected output: Array ["f", "o", "o"]

console.log(Array.from([1, 2, 3], (x) => x + x));
// expected output: Array [2, 4, 6]

console.log(Array.from(new Array(3), (x) => new Array(2)));
//  Array 3 x 2
```

## js 生成二维数组

```javascript
Array.from(new Array(3), (x) => new Array(2));

new Array(3).fill().map((x) => new Array(2));
```

## js 赋值顺序

一开始`从左往右`寻找未声明的变量或者报错信息，若没有这个变量则赋值 undefined，之后`从右往左`进行赋值。

```javascript
var a = { x: 1, y: { z: 2 } };
var b = a;
a.n.e = a.x = { n: 1 }; // => 报错 一开始从左往右a.n被赋值为undefined，而undefined没有e属性所以报错

var a = { x: 1, y: { z: 2 } };
var b = a;
c = a.y.zz = a.d = { n: 2 };
c; // {n:2}
a; // {x:1,y:{z:2,zz:{n:2}},d:{n:2}}

var a = { x: 1, y: { z: 2 } };
var b = a;
b.n = 3;
b; // { x: 1, y: { z: 2 }, n: 3 }
a; // { x: 1, y: { z: 2 }, n: 3 }

var a = { x: 1, y: { z: 2 } };
var b = a;
a.y.xxxx = a.y.zz = a.y = a.d = { n: 2 };
a; // {x: 1, d: { n: 2 }, y: { n: 2 }}
```

**若一行赋值语句中，同时出现父级和子级，则子级赋值默认无效。如`a.y = a.y.zz`，此时无论`a.y`在左边还是右边，赋值`a.y`的时候都会覆盖掉子级的赋值**

**对于共用一条赋值语句的情况，若需要用到前置变量，则尽量将当前语句放到右边**

```javascript
const used = new Array(y).fill().map((z) => new Array(x).fill(0)),
  x = 2,
  y = 3; //  报错: y is not defined

//  正确用法
const x = 2,
  y = 3,
  used = new Array(y).fill().map((z) => new Array(x).fill(0));
```

**https://www.csdn.net/tags/MtTaggwsNjI1NzUtYmxvZwO0O0OO0O0O.html**

## Object.assign 会改变第一个对象

将后面的对象合并到第一个对象中，如果要创建新数组则第一个参数传一个空对象。

```javascript
var a = { a: 1 },
  b = { b: 2 };
var c = Object.assign(a, b);

console.log(c); // {a:1,b:2}
console.log(a); // {a:1,b:2}

var d = { d: 1 },
  e = { e: 2 };
var f = Object.assign({}, d, e);

console.log(f); // {d:1, e:2}
console.log(d); // {d:1}
```

## Input 相关

### Input 加入前缀后缀

用 div 包裹，前缀和后缀用 div 显示，input 在中间。

### Input 宽度随输入内容变化

**https://daotin.netlify.app/winm4g.html#%E6%96%B9%E6%B3%95**

### Fetch 中断请求

**https://github.com/cheungseol/cheungseol.github.io/issues/22**

```javascript
//  在被Promise包围着的Fetch函数中
const FetchFn = async <T>({
  type = 'GET', url, body, isExtraUrl, headers = null, compileBody = true, demandToken,
}: Props) => {
  const fetchController = new AbortController();
  const p = new Promise<T>((resolve, reject) => {
    const requestUrl = isExtraUrl ? url : `${APIURL}${url}`;
    fetch(requestUrl, {
      //  给fetch绑定signal
      signal: fetchController.signal,
      ...
  });
  //  给Promise绑定controller属性
  Object.setPrototypeOf(p, Object.assign(Object.getPrototypeOf(p), { controller: fetchController }));
  return p;
};

//  使用
useEffect(() => {
    const f:any = Fetch({url: `/ln/invoices/${id}`})
    .then(res => {
        console.log(res)
    })
    .catch(e => {
        console.log(e)
    })
    return () => f.controller.abort()
}, [id])
```

## js 中 map()方法是否改变原数组

当数组元素是基本数据类型时，map()方法不会改变原数组；当数组元素是引用类型时，map()方法会改变原数组。  
**https://www.jianshu.com/p/d709fe2be814**

## 解决 js 中小数点太多显示科学计数法的问题

```javascript
new BigNumber(e.fixedSats).div(Math.pow(10, 8)).toFixed();
```

## 字符串比较的 BUG

```javascript
99 > 100; // false
"99" > "100"; // true
```

## 箭头函数不绑定 Arguments 对象

**https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions**

## 临时死区

JavaScript 中的“临时死区”（Temporal Dead Zone，简称 TDZ）是指在代码块中使用 let 或 const 声明变量时，从该声明语句开始直到变量实际被赋值之前的这段时间内，该变量是无法被访问的。

```typescript
//  原代码
const { content }: any =
  (await Fetch({
    url: "/invoices/sendcoins",
    type: "POST",
    body: {
      addr: address,
      memo: address,
      amount,
    },
  })
  (await check2FA(codeFA, content))) &&
  sendCoinByLightning(sendAddress, content, amount, memo); // 报错:Block-scoped variable 'content' used before its declaration.

//  修复
const { content }: any =
  (await Fetch({
    url: "/invoices/sendcoins",
    type: "POST",
    body: {
      addr: address,
      memo: address,
      amount,
    },
  });
  (await check2FA(codeFA, content))) &&
  sendCoinByLightning(sendAddress, content, amount, memo);
```

### 解读

因为 `await Fetch()` 是异步操作，在声明 `content` 变量时，`Fetch` 操作还未完成。

不加入 `;` 会报错是因为 JavaScript 解析器会认为内容还在继续，导致找不到 `content` 变量。

加入 `;` 后，JavaScript 解析器会断定声明结束，`content` 变量等异步操作完成后再使用。

详细解释：

不加入 `;`：

```typescript
const { content }: any = await Fetch({...})
(await check2FA(codeFA, content)) && sendCoinByLightning(...)
```

这时，JavaScript 解析器会把这两个语句认为是一个语句块，认为 `content` 已经声明好了。

但实际上，`await Fetch()` 是异步操作，等异步操作完成后 `content` 才有值，所以会报错找不到 `content`。

加入 `;` 后：

```typescript
const { content }: any = await Fetch({...});
(await check2FA(codeFA, content)) && sendCoinByLightning(...)
```

这时，JavaScript 解析器会把这两个视为两个独立的语句：

1. 声明 `content` 变量
2. 执行 `check2FA` 和 `sendCoinByLightning`

由于 `;` 表示声明结束，所以解析器知道 `content` 还未有值，等异步操作完成后才使用，这样就不会报错。

总的来说，加入 `;` 可以告知解析器 `content` 声明已结束，等异步操作完成后再使用。不加入 `;` 会导致解析器认为 `content` 已有值，进而报错。

## ??与||

```javascript
var a = "" || "error"; // ===> "error"
var b = "" ?? "error"; // ===> ""
```

`??` 检查的是`null/undefined`,""不是这两个值所以返回""。  
`||` 检查的是`truthy/falsy`,""属于 falsy 所以检查下一个值。

## 实现类似Vue的MVVM模式
```javascript
/**
 * Implement a response model similar to MVVM (Model-View-ViewModel) pattern.
 * Model: reactive
 * ViewModel: computed
 * View: console.log
*/

//  确保value初始化时能拿到正确的value以及确保deps不会重复写入副作用函数
let currentEffect = null;

//  计算属性
function computed(getter) {
    let value;
    let needsUpdate = true;

    const effect = () => {
        value = getter();
        needsUpdate = false;
    };

    return {
        get value() {
            if (needsUpdate) {
                currentEffect = effect;
                effect();
                currentEffect = null;
            }
            return value;
        }
    };
}

//  Model
function reactive(target) {
    //  存放每个计算属性出现变更时需要执行的副作用函数
    const deps = {};

    const getDep = (prop) => {
        if (!deps[prop]) {
            deps[prop] = [];
        }
        return deps[prop];
    };

    return new Proxy(target, {
        get(obj, prop) {
            //  只有计算属性初始化时才需要绑定副作用函数
            if (currentEffect) {
                getDep(prop).push(currentEffect);
            }
            return obj[prop];
        },
        set(obj, prop, value) {
            obj[prop] = value;
            //  计算属性出现变更 执行所有绑定的副作用函数
            getDep(prop).forEach(effect => effect());
            return true;
        }
    });
}

const node = reactive({ leftChildren: 1 });
console.log(node.leftChildren, node.rightChildren); // 1 undefined

const children = computed(() => node.leftChildren + (parseInt(node.rightChildren) || 0));
console.log(children.value); // 1

node.leftChildren = 10;
console.log(children.value); // 10

node.rightChildren = 2;
console.log(children.value); // 12

const children2 = computed(() => node.leftChildren * 2);
console.log(children2.value)    // 20

node.leftChildren = 15;
console.log(children.value, children2.value)        //  17 30

node.rightChildren = 5;
console.log(children.value, children2.value)    //  20 30
```


## JavaScript编译原理

### 1.解析
JavaScript引擎会对源代码进行解析，将其转换为抽象语法树（Abstract Syntax Tree, AST）。
- 词法分析: 将源代码转换为一系列的标记（Tokens）。标记是语言的最小单位，如关键字、变量名、操作符等。
- 语法分析: 使用标记生成抽象语法树（AST）

#### AST示例
```javascript
function add(a, b) {
  return a + b;
}

let result = add(2, 3);
console.log(result);
```
对应的AST树
```scss
Program
 ├── FunctionDeclaration (add)
 │   ├── Identifier (a)
 │   ├── Identifier (b)
 │   └── BlockStatement
 │       └── ReturnStatement
 │           └── BinaryExpression (+)
 │               ├── Identifier (a)
 │               └── Identifier (b)
 ├── VariableDeclaration (result)
 │   └── VariableDeclarator
 │       ├── Identifier (result)
 │       └── CallExpression (add(2, 3))
 │           ├── Identifier (add)
 │           ├── Literal (2)
 │           └── Literal (3)
 └── ExpressionStatement (console.log(result))
     └── CallExpression
         ├── MemberExpression (console.log)
         │   ├── Identifier (console)
         │   └── Identifier (log)
         └── Identifier (result)
```

### 2.编译
JavaScript引擎会对AST进行编译，生成中间代码或字节码。
- 字节码生成: 将AST转换为中间表示，然后进一步转换为字节码(字节码是一种低级别的、与机器无关的代码，可以被虚拟机执行)。
- 优化: 在生成字节码的过程中，JavaScript引擎会对代码进行各种优化，以提高执行效率。这些优化可能包括内联函数、消除死代码、常量折叠等。

### 3.执行
最终的字节码由JavaScript引擎的解释器或即时编译器（JIT Compiler）执行。
- 解释器: 直接执行字节码，每次执行都需要重新解释字节码。这种方式启动快，但运行速度较慢。
- 即时编译器（JIT Compiler）: 在运行时将热点代码（Hot Code，即执行频率高的代码）编译为机器码，这种方式启动较慢，但运行速度快。JIT编译器会在执行过程中不断收集代码的运行信息，进行动态优化。


## 作用域
### 查找操作
- LHS: 目的是找到变量容器本身，以便可以对其赋值。例如，在赋值操作的左侧，或作为函数参数传递的变量名。
  - 查找失败：自动隐式 地创建一个全局变量(非严格模式下)，或者抛出 `ReferenceError` 异常(严格模式下)
- RHS: 目的是找到变量的值，即访问或使用变量。例如，在赋值操作的右侧，或在表达式中使用变量时。
  - 不成功的 RHS 引用会导致抛出 `ReferenceError` 异常 

```javascript
function foo(a) { 
  var b = a;
  return a + b; 
}
var c = foo( 2 );

//  LHS: var c、a = 2、 var b
//  RHS: foo(2)、b = a、return a + b(两次、一次找a一次找b)
```

### 作用域嵌套
当一个块或函数嵌套在另一个块或函数中时，就发生了作用域的嵌套。因此，在当前作用域中无法找到某个变量时，引擎就会在外层嵌套的作用域中继续查找，直到找到该变量，抵达最外层的作用域(也就是`全局作用域`)为止。

### 欺骗词法作用域
主要用到`eval`和`with`这两个关键词，但是这两个关键词在严格模式和后续的ECMA版本中都被禁用和废除了。
```javascript
function foo(str, a) { 
  eval( str ); // 欺骗! 
  console.log( a, b );
}
var b = 2;
foo( "var b = 3;", 1 ); // 1, 3
```

```javascript
function foo(obj) { 
  with (obj) {
    a = 2; 
  }
}
var o1 = { 
  a: 3
};
var o2 = { 
  b: 3
};
foo( o1 );
console.log( o1.a ); // 2
foo( o2 );
console.log( o2.a ); // undefined
console.log( a ); // 2——不好，a 被泄漏到全局作用域上了!
```
使用`eval`和`with`会影响性能表现。       
**这两个机制的副作用是引擎无法在编译时对作用域查找进行优化，因为引擎只能谨慎地认为这样的优化是无效的。最悲观的情况是如果出现了`eval(..)`或`with`，所有的优化可能都是无意义的，因此最简单的做法就是`完全不做`任何优化。**

### 函数声明和表达式
区分函数声明和表达式最简单的方法是看 function 关键字出现在声明中的位置(不仅仅是一行代码，而是整个声明中的位置)。如果 function 是声明中的第一个词，那么就是一个函数声明，否则就是一个函数表达式。
```javascript
//  函数声明
function test () {...}

//  函数表达式 IIFE1
(function IIFE() {...})()

//  IIFE2
(function() {...}())

//  IIFE3
void function() { ... }();
```

### 提升
只有`声明`本身会被提升，而赋值或其他运行逻辑会留在原地。**(所以这里需要区分函数声明和函数表达式)**    
也就是说函数声明会被提升，但是函数表达式并不会被提升。    
```javascript
foo()

function foo() {
  console.log( a ); // undefined 
  var a = 2;
}

/**   实际的执行顺序  */
function foo() { 
  var a;
  console.log( a ); // undefined
  a = 2; 
}

foo();
```
```javascript
foo(); // TypeError
bar(); // ReferenceError
var foo = function bar() { 
  // ...
};

/**   实际的执行顺序  */
var foo;
foo(); // TypeError
bar(); // ReferenceError
foo = function() {
  var bar = ...self... 
  // ...
}
```
函数声明和变量声明都会被提升，但是函数会`首先`被提升，然后才是变量。
```javascript
foo() // 1

var foo;

function foo() {
  console.log(1)
}

foo = function() {
  console.log(2)
}

/**   实际的执行顺序  */
function foo() {
  console.log(1)
}

foo() // 1

foo = function () {
  console.log(2)
}
```
出现在后面的函数声明可以`覆盖`前面的。
```javascript
foo() // 3
function foo() {
  console.log(1)
}

var foo = function () {
  console.log(2)
}

function foo () {
  console.log(3)
}
```

## this丢失
```typescript
const handlerAction = async (action: string) => {
  const handlerMapping = {
    [HiddenAction.Selected]: documentSchduler.handler1
  }
  await handlerMapping[action]()       //  handler1内部的this指向global 严格模式下指向undefined
}
```

```typescript
const handlerAction = async (action: string) => {
  const handlerMapping = {
    [HiddenAction.Selected]: documentSchduler.handler1
  }
  await documentSchduler.handler1()       //  handler1内部的this指向documentSchduler
}
```

**https://github.com/felix-cao/Blog/issues/90**
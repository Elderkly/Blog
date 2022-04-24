## 19.7.24  
### 浏览器渲染流程  
1.处理HTML并构建DOM树  
2.处理CSS构建CSSOM树  
3.将DOM与CSSOM合并成一个渲染树  
4.根据渲染树来布局,计算每个节点的位置  
5.调用GPU绘制,合并图层,显示在屏幕上  
   
### 阻塞渲染  
在构建CSSOM树时，会阻塞渲染，直到CSSOM树构建完毕。  
当HTML解析到script标签时，会暂停构建DOM，直到解析完后才会在暂停的地方重新开始。  
并且CSS也会影响JS的执行，只有解析完样式表才会去执行JS，所以也可以认为这种情况下，CSS也会暂停构建DOM。  

head内的js脚本会阻塞body标签的渲染，把js脚本放在head内需要等到js执行完毕后才会渲染body。  
而将样式文件写在head中是为了避免页面元素由于样式缺失造成的白屏现象。  

https://yuchengkai.cn/docs/frontend/browser.html#%E6%B8%B2%E6%9F%93%E6%9C%BA%E5%88%B6

## 19.7.26
### 浏览器事件循环
JS中有一个主线程和调用栈，所有的任务都会放入到调用栈里面等待主线程执行。  
调用栈：后进先出，当函数被调用时，会被添加到栈中的顶部，执行完毕后从顶部移除这个函数，直到调用栈被清空。   
任务队列：先进先出，如果是同步函数则会在调用栈中按顺序被主线程调用，如果是异步函数则会在异步有了结果之后，将注册的回调函数放入到任务队列中，等到调用栈被清空了，也就是主线程空闲的时候，被读取到调用栈中等待执行。   

宏任务：包括整体代码script，setTimeout,setInterval等   
微任务：Promise，process.nextTick等   
进入整体代码（宏任务）后，开始第一次循环，执行所有的微任务。接着再次从宏任务开始，找到其中一个宏任务队列执行完毕，再执行所有微任务。以此循环。

**调用栈中的同步任务都执行完毕了，栈内就清空了，代表主线程空闲了，就会去任务队列按顺序读取一个任务放入栈中执行。每次栈内被清空，就会去读取任务队列还有没有任务，有就读取执行，一直循环读取-执行的操作，这就形成了事件循环。**
   
**对于setInterval(fn,ms)来说，不是每过ms秒会执行一次fn，而是每过ms秒，会有fn进入Event Queue。一旦setInterval的回调函数fn执行时间超过了延迟时间ms，那么就完全看不出来有时间间隔了。**

![事件循环](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2017/11/21/15fdcea13361a1ec~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)
https://github.com/aooy/blog/issues/5   
https://juejin.im/post/59e85eebf265da430d571f89   
https://www.cnblogs.com/yqx0605xi/p/9267827.html

## 19.7.29
### 图层
一般来说，可以把普通文本流看成一个图层，特定的属性可以生成一个新的图层。不同的图层渲染互不影响，所以对于某些频繁需要渲染的建议单独生成一个新图层，提高性能。但也不能生成过多的图层，会引起反作用。     
通过以下几个属性可以生成新图层  
* 3D变换：translate3d、translateZ
* will-change
* video、iframe标签
* 通过动画实现的opacity动画转换
* position:fixed

### 重绘和回流
重绘是当前节点需要更改外观而不会影响布局的，比如改变color就称为重绘   
回流是布局或者几何属性需要改变就称为回流   
**回流一定会发生重绘，重绘不一定会引起回流。回流所需的成本比重绘高的多，改变深层次的节点可能会导致父节点的一系列回流。**  
**减少重绘和回流**  
1.用translate替代top  
2.用visibility替代display：none，前者只会引起重绘，后者改变布局会引起回流      
3.先把DOM给隐藏了(display：none)了，接着对DOM进行若干修改后，再将DOM显示出来   
4.不要使用table布局(tr,td)，可能很小的一个改动会引起整个table的重新布局  
5.将频繁运行的动画变为图层，图层能够阻止该节点回流影响到别的元素   

https://yuchengkai.cn/docs/frontend/browser.html#%E5%9B%BE%E5%B1%82  

## 19.7.30
### 跨域
浏览器出于安全考虑，有同源策略。也就是说域名、端口、协议有一个不同就是跨域。（IE无视端口规则，即同域名下不同端口间可以正常请求）  
**如何解决跨域**  
* JSONP：利用<script>标签没有跨域限制的漏洞。通过<script>标签指向一个需要访问的地址并提供一个回调函数来接收数据。  
JSONP只限于get请求   
* CORS:服务端设置Access-Control-Allow-Origin就可以开启CORS。该属性表示哪些域名可以访问资源，如果设置通配符则表示所有网站都可以访问资源。  
* document.domain:该方法只能应用于二级域名相同的情况下，比如id.qq.com和game.qq.com适用该方法。   
只需要给页面添加document.domain = 'qq.com'表示二级域名都相同就可以实现跨域。   
* postMessage:常用于获取嵌入页面中的第三方页面数据，一个页面发送消息，另一个页面判断来源，并接收消息。   

https://yuchengkai.cn/docs/frontend/browser.html#%E8%B7%A8%E5%9F%9F

## 19.8.1
### new 的过程
1.新生成一个对象   
2.链接到原型   
3.绑定this   
4.返回新对象   

### 原型链
![原型链](https://camo.githubusercontent.com/8c32afe801835586c6ee59ef570fe2b322eadd6e/68747470733a2f2f79636b2d313235343236333432322e636f732e61702d7368616e676861692e6d7971636c6f75642e636f6d2f626c6f672f323031392d30362d30312d3033333932352e706e67)
```javascript
// function(){} 为构造函数
const fn = function() {}
//  prototype 指向原型（一个对象） {constructor: ƒ}
fn.prototype
//  constructor  指向原型的构造函数 
fn.prototype.constructor === fn
//  __proto__  指向创建该对象的构造函数的原型  即Function.prototype
fn.__proto__  === Function.prototype
//  访问创建fn的构造函数 即Function(){} 
fn.__proto__.constructor === Function

// 创建一个对象 
const obj = {a:1}
// 对象由Object(){}创建 
obj.constructor === Object
//  对象没有prototype属性
obj.prototype === undefined
// __proto__ 指向创建该对象的构造函数的原型 即Object.prototype
obj.__proto__ === Object.prototype
```
每一个函数都有`prototype`属性，该属性指向原型。除了Function.prototype.bind()，通过bind方法生成的函数没有`prototype`属性。    
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
   
https://github.com/KieSun/Dream/issues/2

## 19.8.5
### 安全
#### XSS
XSS通过修改HTML节点或者执行JS代码来攻击网站。   
通常的防御手段是转义输入的内容，对引号、尖括号、斜杠进行转义。   
例如通过URL获取某些参数   
```html
<!-- http://www.domain.com?name=<script>alert(1)</script> -->
<div>{{name}}</div>
```
#### CSRF
CSRF就是利用用户的登录状态发起恶意请求。  
如果是Get请求则可以在img标签中设置图片地址为对应接口，如果是Post请求则需要用表单来提交接口。  
**如何防御**   
1.Get请求不对数据进行修改。   
2.Cookie设置`SameSite`属性，使Cookie不随着跨域请求发送。  
3.阻止第三方网站请求接口。  
4.请求时附带验证信息，如token。  
5.验证Referer。浏览器发送请求时会带上Referer，通过验证Referer判断请求是否是第三方网站发起的。  

#### CSP
CSP本质上是建立白名单，规定浏览器只能执行特定来源的代码。   
通常可以在HTTP Header（请求头）或者HTML的meta标签中设置`Content-Security-Policy`（只允许加载本站资源/只加载HTTPS协议图片/允许加载任何来源框架）来开启CSP。   

https://yuchengkai.cn/docs/frontend/safety.html#xss

### H5新特性
1.video/radio   
2.canvas   
3.webSocket   
4.webWorker js多线程   
5.语义化标签如header、footer、nav等   
6.新增了很多表单属性如min和max、autofocus、placehoder等   
7.sessionStorage 短期存储浏览器关闭就删除;localStorage 长期数据存储，与cookie相比cookie大小只有4kb左右，而localStorage有5Mb。

### 继承
使用call或apply借用其他构造函数的成员。
```javascript
//  父类
function Person(name) {
    this.name = name
    this.attr = ['小黄','小白']
    this.print = () => {
        console.log(this.name)
    }
}
//  子类
function Student(name) {
    Person.call(this,name)
}

const a = new Person('A')
a.print()            // A
const b = new Student('B')
b.print()            // B
b.attr.push('小黑');
console.log(b.attr)  // ["小黄", "小白", "小黑"]
console.log(a.attr)  // ["小黄", "小白"]
```
https://www.jianshu.com/p/b76ddb68df0e  

### 深拷贝浅拷贝
```javascript
//  浅拷贝
const a = {a:1,b:2,c:3}
const b = a
b.d = 4
console.log(b)      //  {a: 1, b: 2, c: 3, d: 4}
console.log(a)      //  {a: 1, b: 2, c: 3, d: 4}

//  深拷贝
function clone(num) {
    let newNum
    if (num instanceof Array) {
        newNum = []
        num.map((e,index) => newNum[index] = clone(num[index]))
        return newNum
    } else if (num instanceof Object) {
        newNum = {}
        for (let i in num) {
            newNum[i] = clone(num[i])
        }
        return newNum
    } else {
        return num
    }
}
const c = clone(a)
c.e = 5
console.log(c)      //  {a: 1, b: 2, c: 3, d: 4, e: 5}
console.log(a)      //  {a: 1, b: 2, c: 3, d: 4}
```
### super()
说明：`super`是es6新增的语法糖 用于访问父类。     
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


### 实现毛玻璃效果
```css
background: rgba(255,255,255,.2);
backdrop-filter: saturate(180%) blur(20px);
```

## 浏览器缓存
**Web缓存种类：** 数据库缓存，CDN缓存，代理服务器缓存，浏览器缓存。   
**浏览器缓存过程：** 强缓存，协商缓存。   
**浏览器缓存位置一般分为四类：** Service Worker-->Memory Cache-->Disk Cache-->Push Cache。
### 浏览器缓存相关字段
![字段](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0c82d0049c3f4f57bf66d8effcb25ed5~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
### 缓存分类
![缓存分类](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/70f599db34fa42068ccfa4e04748a078~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
|名称|用途|
|:-:|:-:|
|Service Worker|是运行在浏览器背后的独立线程，一般可以用来实现缓存功能，只支持HTTPS|
|Memory Cache|存放于内存中的缓存，大多用于存放样式、脚本文件，存放时间短，随着进程释放而释放|
|Disk Cache|存放于硬盘中的缓存，大多用于存放图片、视频资源等，存放时间长，容量大|
|prefetch cache|prefetch是预加载的一种方式，被标记为prefetch的资源，将会被浏览器在空闲时间加载|
|Push Cache|HTTP2的内容，在其他缓存没命中的情况下使用，存放时间短，随着进程释放而释放|
### 缓存过程
#### 强缓存
首次请求：如果响应头中`expires`、`pragma`或者`cache-control`字段，代表这是强缓存，浏览器就会把资源缓存在memory cache 或 disk cache中。    
第二次请求：如果符合强缓存条件就直接返回状态码200，从本地缓存中拿数据。否则把响应参数存在request header请求头中，看是否符合协商缓存，符合则返回状态码304，不符合则服务器会返回全新资源。    
![强缓存](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ca00bff3081e4cfd993a8f252f4fa23a~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

#### 协商缓存
协商缓存就是强缓存失效后，浏览器携带缓存标识向服务器发送请求，由服务器根据缓存标识来决定是否使用缓存的过程。   
服务器资源未更新：返回304，读取缓存    
服务器资源更新：重新请求，返回200    
**实现协商缓存:**   
* Last-Modified / If-Modified-Since：服务端返回Last-Modified即文件最后修改时间，客户端请求时将其写入请求头的If-Modified-Since字段，服务端对比文件修改时间，若服务端文件修改时间大于If-Modified-Since则重新返回资源和200状态码，否则返回304，代表资源无更新，可继续使用缓存文件。
* Etag / If-None-Match：服务端返回Etag字段即服务器生成的文件唯一标识，客户端将其写入If-None-Match字段中，服务端收到后判断客户端的If-None-Match与服务端文件的唯一标识是否一致，一致则返回304，否则返回200.   
    
**Etag / If-None-Match优先级高于Last-Modified / If-Modified-Since，同时存在则只有Etag / If-None-Match生效。**

### 强缓存与协商缓存的区别
1. 强缓存不发请求到服务器，所以有时候资源更新了浏览器还不知道，但是协商缓存会发请求到服务器，所以资源是否更新，服务器肯定知道。   
2. 大部分web服务器都默认开启协商缓存。   
### 刷新对于强缓存和协商缓存的影响
1. 当ctrl+f5强制刷新网页时，直接从服务器加载，跳过强缓存和协商缓存。   
2. 当f5刷新网页时，跳过强缓存，但是会检查协商缓存。   
3. 浏览器地址栏中写入URL，回车 浏览器发现缓存中有这个文件了，不用继续请求了，直接去缓存拿。（最快）   
      
https://juejin.cn/post/6947936223126093861



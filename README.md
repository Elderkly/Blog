# WebNote
笔记

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
```javascript
// function(){} 为构造函数
const fn = function() {}
//  prototype 指向原型（一个对象） {constructor: ƒ}
fn.prototype
//  constructor  指向原型的构造函数 
fn.prototype.constructor === fn
// __proto__  指向创建该对象的构造函数的原型  即Function.prototype
fn.__proto__  === Function.prototype
// 访问创建fn的构造函数 即Function(){} 
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
每一个函数都有`prototype`属性，该属性指向原型。除了Function.prototype.bind()，通过bind函数生成的函数没有`prototype`属性。    
每一个对象都有`__proto__`属性，指向创建该对象的构造函数的原型。  
`Function.prototype`和`Object.prototype`是两个特殊的对象，他们由引擎来创建。  
函数的`prototype`是一个对象，也就是原型。  
对象的`__proto__`指向原型，`__proto__`将对象和原型连接起来组成了原型链。  

#### Function.proto === Function.prototype
所有对象都可以通过原型链最终找到`Object.prototype`，虽然`Object.prototype`也是一个对象，但是这个对象不是`Object`创建的，而是引擎自己创建的`Object.prototype`。  
**所以可以这么说，所有实例都是对象，但是对象不一定都是实例。**  
`Function.prototype`这个实例其实是个函数，这个函数也是引擎自己创建的。  
首先引擎创建了`Object.prototype`，接着创建`Function.prototype`,并且用`__proto__`将两者连接起来。  
**所以得出结论，不是所有函数都是`new Function()`产生的**  
有了`Function.prototype`后才有了`function Function(){}`，然后其他的构造函数都是`Function()`生成的。  
由于其他构造函数都可以通过原型链找到`Function.prototype`，并且`function Function()`本质也是函数，为了不产生混乱就将`function Function()`的`__proto__`联系到`Function.prototype`上。  
   
https://github.com/KieSun/Dream/issues/2


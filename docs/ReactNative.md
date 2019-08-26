## react-native优缺点
优点：好上手，学习周期短，一套代码可以编译两个平台，对于一些公用的业务需求，用RN开发不用两个平台区分修改，更加方便维护和管理。   
缺点：前端开发人员很难精通这个框架，如果想学会如何使用这个框架可以在很短时间内上手，但是如果想深入了解RN的实现原理就显得有点力不从心了，如果想精通RN则绕不过原生开发，有这个时间倒不如专心研究一个平台。二是对于一些复杂的组件使用RN实现会出现性能问题，特别是在安卓中低端机型上面。

## 接入原生组件
### 安卓
UI组件：通过`requireNativeComponent`API引入组件。    
原生模块：使用`NativeModules.ModelName`来访问原生组件。    
事件监听：使用`DeviceEventEmitter`监听。   

### IOS
UI组件：与安卓相同使用`requireNativeComponent`引入组件。    
原生模块：与安卓一样使用`NativeModules.ModelName`来访问原生组件。    
事件监听：与安卓不同，这里使用`NativeEventEmitter`来监听事件，实例化`NativeEventEmitter`模块，并且将原生模块名传入，最后通过实例化后的`addListener`方法传入需要监听的事件名称和回调函数即可监听事件。   

  

 


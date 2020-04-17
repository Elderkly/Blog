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

# Android TV

## 监听遥控器操作
```javascript
  this.tvEventHandler = new TVEventHandler();
  this.tvEventHandler.enable(
    this, (cmp, evt) =>  {
      const {eventType, eventKeyAction} = evt
      //  只有按键按下触发 防止重复执行
      if (eventKeyAction !== 0) return
      if (evt && eventType === 'right') {
        DeviceEventEmitter.emit('TVEventHandler','right')
      } else if(evt && eventType === 'up') {
        DeviceEventEmitter.emit('TVEventHandler','up')
      } else if(evt && eventType === 'left') {
        DeviceEventEmitter.emit('TVEventHandler','left')
      } else if(evt && eventType === 'down') {
        DeviceEventEmitter.emit('TVEventHandler','down')
      } else if(evt && eventType === 'playPause') {
        DeviceEventEmitter.emit('TVEventHandler','playPause')
      }
    }
```

##  禁用控件聚焦
### `accessibilityStates`:       
`['disabled']`: 禁用     
`['selected']`: 允许聚焦   

##  自动聚焦
### `hasTVPreferredFocus`: `true` / `false`

##  禁用元素某个方向上的聚焦动作/指定聚焦元素
```javascript
    const ID = findNodeHandle(DomRef)
    const ID2 = findNodeHandle(Dom2Ref)
    Dom.setNativeProps(
        {
            nextFocusLeft: ID,          //  将ID指定为自身 即禁用该方向          
            nextFocusUp: ID2,           //  指定为其他元素 则会自动聚焦到该元素身上
            nextFocusDown -1,           //  指定为-1或不存在的ID 则置为默认操作
            nextFocusRight: -1, 
        },
    );

    //  禁用左右方向  向下聚焦到Dom2
    Dom.setNativeProps(
        {
            nextFocusLeft: ID,                 
            nextFocusRight: ID, 
            nextFocusDown: ID2,
        },
    );
    //  恢复左右方向聚焦
    Dom.setNativeProps(
        {
            nextFocusLeft: -1,                
            nextFocusRight: -1, 
            nextFocusDown: ID2,
        },
    );
```

##  在`ScrollView`中的元素 聚焦后滚动位置不正确
将元素的外边距`margin`改为内边距`padding`,这样子聚焦后ScrollView就会滚动到包含元素内边距的位置

##  打开二级页面后焦点还能回到一级页面
1.用`Modal`组件盖住一级页面可以直接禁用一级页面的聚焦，但是由于`Modal`是顶层组件，所以需要将所有页面都用`Modal`包装，否则会出现二级页面无法盖住一级页面的BUG。
2.采用原生多路由方案，即通过原生路由来打开RN页面，可完美解决该问题。

 


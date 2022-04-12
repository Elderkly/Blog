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

## `TouchableOpacity`组件的自动聚焦属性`hasTVPreferredFocus`可能会带来严重的性能问题
在需要频繁地通过`hasTVPreferredFocus`设置聚焦元素的场景中，如果频繁修改该属性会出现焦点在元素间自动鬼畜切换的BUG，解决这个问题的思路就是在父组件聚焦时，将其中的一个子组件的`hasTVPreferredFocus`设置为`true`,之后就不再调整这个属性了,等到下一次父组件聚焦再设置一个子组件自动聚焦，以此为周期。

##  在部分模拟器上不触发`onFocus`和`onBlur`事件
**巨坑!!!!!!!!!**    
`TouchableOpacity`源码
```javascript
//  位置：node_modules/react-native/Libraries/Components/Touchable/Touchable.js:373
  componentDidMount: function() {
    if (!Platform.isTV) {
      return;
    }
    this._tvEventHandler = new TVEventHandler();
    this._tvEventHandler.enable(this, function(cmp, evt) {
      const myTag = ReactNative.findNodeHandle(cmp);
      evt.dispatchConfig = {};
      if (myTag === evt.tag) {
        if (evt.eventType === 'focus') {
          cmp.touchableHandleFocus(evt);
        } else if (evt.eventType === 'blur') {
          cmp.touchableHandleBlur(evt);
        } else if (evt.eventType === 'select') {
          cmp.touchableHandlePress &&
            !cmp.props.disabled &&
            cmp.touchableHandlePress(evt);
        }
      }
    });
  }
```
RN根据`TVEventHandler`的回调判断元素是聚焦还是失焦，而RN为了区分手机版跟TV版，只有`Platform.isTV`为`true`才会创建监听，但是部分机顶盒例如天猫魔盒对`isTV`的判断会出现为`false`的情况，导致了在TV端上无法实现聚焦和失焦的监听。     
要想解决这个问题，只需要进入`node_modules/react-native/Libraries/Utilities/Platform.android.js`将`isTV`方法的返回值改为`true`即可。


#  `react-native-image-picker`组件的相关问题
版本:    
`"react-native": "0.61.1",`      
`"react-native-image-picker": "1.1.0",`     

#### 针对ios选择iCloud图片引起崩溃的问题:
```
    //  node_modules/react-native-image-picker/ios/ImagePickerManager.m:380
             [data writeToFile:path atomically:YES];

             if (![[self.options objectForKey:@"noData"] boolValue]) {
-                NSString *dataString = [data base64EncodedStringWithOptions:0]; // base64 encoded image string
-                [self.response setObject:dataString forKey:@"data"];
+                NSString *dataString = [data base64EncodedStringWithOptions:0];
+                NSLog(@"ERROR IMG");
+                [self.response setObject:@"" forKey:@"uri"];
             }

             BOOL vertical = (editedImage.size.width < editedImage.size.height) ? YES : NO;
```

#### 针对华为机型设置`mediaType:'photo'`后还能选择视频的问题 选择视频后引起崩溃的问题：
```
    //  node_modules/react-native-image-picker/android/src/main/java/com/imagepicker/ImagePickerModule.java:475
     else
     {
       imageConfig = getResizedImage(reactContext, this.options, imageConfig, initialWidth, initialHeight, requestCode);
-      if (imageConfig.resized == null)
+      if (imageConfig == null) {
+        imageConfig = new ImageConfig(null, null, 0, 0, 100, 0, false);
+        responseHelper.putString("error", "illegal file format");
+        responseHelper.invokeResponse(callback);
+        callback = null;
+        this.options = null;
+        return;
+      }
+      else if (imageConfig.resized == null)
       {
         removeUselessFiles(requestCode, imageConfig);
         responseHelper.putString("error", "Can't resize the image");
```


###  针对安卓设备上无法访问配置了自签名证书的服务器       
https://stackoverflow.com/a/60116643

#   如何调整文件结构



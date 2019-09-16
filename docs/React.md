### 说说react的虚拟dom
通过babel将JSX编译成`React.createElement`的形式，通过这个方法返回的对象记录了这个DOM节点的所有信息，这个记录信息的对象我们称之为**虚拟DOM**。

### react的diff算法
为了减少Dom更新，我们需要找出渲染前后真正变化的部分，只需要更新这一部分Dom。而对比变化，找出需要更新部分的算法我们称之为**diff算法**   
diff算法的实现是对比当前的真实DOM和虚拟DOM，在对比过程中直接更新虚拟DOM，并且只对比同一层级的变化。   

1.对比两棵树的跟节点类型，如果类型不同，则将原来的摧毁掉创建一个新的，并且将原来那个元素的子节点插入到新节点中。   
2.如果两个节点的类型相同，则更新他的属性，找出前后不同的属性，用新的属性覆盖它。   
3.递归对比子节点   

### react的setstate
setState之所以是异步的，最大原因是为了性能提升。  
state可能会异步更新，所以不要依赖他的值来更新下一个状态。  
setState也可以接收一个函数，在这个函数可以拿到先前的状态，并通过这个函数的返回值得到下一个状态。   
setState的异步可以通过Promise或者setTimeout来实现。   

### react生命周期
shouldComponentUpdate:当props和state发生变化时，`shouldComponentUpdate`会在渲染前被调用，默认返回true，如果返回false则会拦截更新。   
componentWillReceiveProps:父组件重新传递props时就会调用这个函数。   

### redux
store：是一个对象，它保存整个应用的state。可以通过`getState()`访问state，通过`dispatch(action)`改变state。   
action:是一个对象，描述已发生事件的普通对象，它们必须有一个`type`属性表明正在执行的 action 的类型。    
reducer:是一个纯函数，接收先前的state和action，并且返回新的state。       

### redux的更新流程
触发action后，将原先的store和action传给reducer，经过计算后返回新的store。   
通过`store.dispatch(action)`触发action后，store会自动调用reducer，并且传入当前state和action，reducer会返回新的state。state一旦有变化，就会调用监听函数`store.subscribe(function)`。如果是react，这个时候就会触发重新渲染View。   
https://www.cnblogs.com/goodjobluo/p/9077010.html      
http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html

### redux thunk
常规`action`只会返回一个对象，里面包含type值以及要修改的参数。    
在`redux`的`createStore`中使用`applyMiddleware`引入`redux-thunk`中间件后，    
允许`action`返回一个函数，这个函数接受两个参数`dispatch`和`getState`。   
```javascript
//  常规action 请求开始
function AjaxStart() {
    return {
        type: AJAX_START
    }
}
//  请求成功
function AjaxSuccess(data){}
//  请求失败
function AjaxError(message) {}

//  异步action
function GetData(){
    return dispath => {
        //  请求开始前触发action修改state为请求中
        dispath(AjaxStart())
        //  发起请求
        fetch('https://www.baidu.com')
        //  请求成功 修改state为请求完成 将数据存入state
            .then( res => dispath(AjaxSuccess(res)))
        //  请求失败 修改对应state
            .catch( error => dispath(AjaxError(error)))
    }
}
```
https://www.jianshu.com/p/061d084d1408   
https://segmentfault.com/q/1010000013653435   




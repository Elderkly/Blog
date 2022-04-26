## Raect
### react的虚拟dom
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

### setState什么时候是同步的什么时候是异步的
异步：由React控制或封装的函数例如onChange、onClick、onTouchMove等，以及React的生命周期内setState也是异步的。   
```javascript
constructor() {
  this.state = {
    count: 10
  }

  this.handleClickOne = this.handleClickOne.bind(this)
  this.handleClickTwo = this.handleClickTwo.bind(this)
}

handleClickOne() {
  this.setState({ count: this.state.count + 1})
  console.log(this.state.count)     //  10
}

render() {
  return (
    <button onClick={this.hanldeClickOne}>clickOne</button>
    <button onClick={this.hanldeClickTwo}>clickTwo</button>
    <button id="btn">clickTwo</button>
  )
}
```
同步：React控制之外的事件中调用setState是同步更新的。比如通过addEventListener绑定的事件，setTimeout/setInterval等。
```javascript
componentDidMount() {
  document.getElementById('btn').addEventListener('clcik', () => {
    this.setState({ count: this.state.count + 1})
    console.log(this.state.count)       //  11
  })
}

handleClickTwo() {
  setTimeout(() => {
    this.setState({ count: this.state.count + 1})
    console.log(this.state.count)       //  11
  }, 10)  
}
```
https://www.jianshu.com/p/799b8a14ef96     

### React是怎样控制异步和同步的呢？
在 React 的 setState 函数实现中，会根据一个变量 isBatchingUpdates 判断是直接更新 this.state 还是放到队列中延时更新，而 isBatchingUpdates 默认是 false，表示 setState 会同步更新 this.state；但是，有一个函数 batchedUpdates，该函数会把 isBatchingUpdates 修改为 true，而当 React 在调用事件处理函数之前就会先调用这个 batchedUpdates将isBatchingUpdates修改为true，这样由 React 控制的事件处理过程 setState 不会同步更新 this.state。   
![React是怎样控制异步和同步的呢](https://upload-images.jianshu.io/upload_images/5256541-992ce78e70151b57.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/720/format/webp)   

### react生命周期
shouldComponentUpdate:当props和state发生变化时，`shouldComponentUpdate`会在渲染前被调用，默认返回true，如果返回false则会拦截更新。   
componentWillReceiveProps:父组件重新传递props时就会调用这个函数。   

#### 挂载
* constructor()：需调用super(props)，否则拿不到this.props，不要在这里使用setState()
* static getDerivedStateFromProps()：在shouldComponentUpdate之前调用，返回一个对象来更新state，返回null则不更新，替代原来的componentWillReceiveProps，是个静态函数，无法使用this。
* render()：必须实现的函数
* componentDidMount()：组件挂载后调用，可在此进行订阅
#### 更新
* static getDerivedStateFromProps()
* shouldComponentUpdate()：当 props 或 state 发生变化时，在渲染前调用，若返回false则不会触发更新
* render()
* getSnapshotBeforeUpdate()：更新视图之前调用，可在此访问到DOM，并将返回值返回给componentDidUpdate。
* componentDidUpdate()：更新后调用，首次渲染不调用
#### 卸载
* componentWillUnmount()：卸载前调用，在此取消订阅
#### 错误处理
* static getDerivedStateFromError()：会在渲染阶段调用，因此不允许出现副作用
* componentDidCatch()：会在“提交”阶段被调用，因此允许执行副作用

#### render阶段与commit阶段
可以理解为render阶段执行数据处理，筛选出需要更新的部分，commit阶段则是将render阶段处理得出的数据进行DOM修改。

### ref
绑定：元素添加属性`ref={this.textInput}`，在`constructor`中声明`this.textInput = React.createRef()`，之后便可通过`this.textInput`访问DOM。

### 绑定事件
```javascript
export default class Test extends Component {
  constructor(props) {
    super(props)
    this.handleClick1 = this.handleClick1.bind(this)
  }


  handleClick1() {
    //...
  }

  handleClick2 = () => {
    //...
  }

  handleClick3() {
    //...
  }

  handleClick4() {
    //...
  }

  render() {
    return (
      <div>
        <div onClick={this.handleClick1}>方法1</div>
        <div onClick={this.handleClick2}>方法2</div>
        <div onClick={this.handleClick3.bind(this)}>方法3</div>
        <div onClick={() => this.handleClick4())}>方法4</div>
      </div>
    )
  }
}
```
对于方法3和方法4，每次渲染这个组件的时候，都会创建不同的回调函数，更推荐方法1和方法2。   

## Redux
store：是一个对象，它保存整个应用的state。可以通过`getState()`访问state，通过`dispatch(action)`改变state。   
action:是一个对象，描述已发生事件的普通对象，它们必须有一个`type`属性表明正在执行的 action 的类型。    
reducer:是一个纯函数，接收先前的state和action，并且返回新的state。       

### 为什么要用redux而不使用context
1.redex修改state需要通过触发action修改,相对context直接赋值state来说会更规范一些也更安全一些    
2.context无法使用redux的中间件    

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

# React Hook
```typescript jsx
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // 相当于 componentDidMount 和 componentDidUpdate:
  useEffect(() => {
    // 使用浏览器的 API 更新页面标题
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```
### useState 方法的返回值是什么?
返回值为：当前 `state` 以及更新 `state` 的函数。这就是我们写 `const [count, setCount] = useState()` 的原因。这与 class 里面 this.state.count 和 this.setState 类似，唯一区别就是你需要成对的获取它们。
### 为什么在组件内部调用 useEffect？
将 useEffect 放在组件内部让我们可以在 effect 中`直接访问` count state 变量（或其他 props）。
### useEffect 会在每次渲染后都执行吗？
是的，默认情况下，它在`第一次渲染之后和每次更新之后`都会执行。（我们稍后会谈到如何控制它。）你可能会更容易接受 effect 发生在“渲染之后”这种概念，不用再去考虑“挂载”还是“更新”。React 保证了每次运行 effect 的同时，DOM 都已经更新完毕。
### 为什么要在 effect 中返回一个函数?
这是 effect 可选的清除机制。每个 effect 都可以返回一个`清除函数`。如此可以将添加和移除订阅的逻辑放在一起。它们都属于 effect 的一部分。
### React 何时清除 effect？
React 会在组件卸载的时候执行清除操作。正如之前学到的，effect 在每次渲染的时候都会执行。这就是为什么 `React 会在执行当前 effect 之前对上一个 effect 进行清除`。    

**effect中返回的清除函数获取的参数是上一次effect的参数**     
https://react.docschina.org/docs/hooks-overview.html




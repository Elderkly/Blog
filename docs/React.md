## Raect

### react 的虚拟 dom

通过 babel 将 JSX 编译成`React.createElement`的形式，通过这个方法返回的对象记录了这个 DOM 节点的所有信息，这个记录信息的对象我们称之为**虚拟 DOM**。

### react 的 diff 算法

为了减少 Dom 更新，我们需要找出渲染前后真正变化的部分，只需要更新这一部分 Dom。而对比变化，找出需要更新部分的算法我们称之为**diff 算法**  
diff 算法的实现是对比当前的真实 DOM 和虚拟 DOM，在对比过程中直接更新虚拟 DOM，并且只对比同一层级的变化。

1.对比两棵树的跟节点类型，如果类型不同，则将原来的摧毁掉创建一个新的，并且将原来那个元素的子节点插入到新节点中。  
2.如果两个节点的类型相同，则更新他的属性，找出前后不同的属性，用新的属性覆盖它。  
3.递归对比子节点

### react 的 setstate

setState 之所以是异步的，最大原因是为了性能提升。  
state 可能会异步更新，所以不要依赖他的值来更新下一个状态。  
setState 也可以接收一个函数，在这个函数可以拿到先前的状态，并通过这个函数的返回值得到下一个状态。  
setState 的异步可以通过 Promise 或者 setTimeout 来实现。

### setState 什么时候是同步的什么时候是异步的

异步：由 React 控制或封装的函数例如 onChange、onClick、onTouchMove 等，以及 React 的生命周期内 setState 也是异步的。

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

同步：React 控制之外的事件中调用 setState 是同步更新的。比如通过 addEventListener 绑定的事件，setTimeout/setInterval 等。

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

### React 是怎样控制异步和同步的呢？

在 React 的 setState 函数实现中，会根据一个变量 isBatchingUpdates 判断是直接更新 this.state 还是放到队列中延时更新，而 isBatchingUpdates 默认是 false，表示 setState 会同步更新 this.state；但是，有一个函数 batchedUpdates，该函数会把 isBatchingUpdates 修改为 true，而当 React 在调用事件处理函数之前就会先调用这个 batchedUpdates 将 isBatchingUpdates 修改为 true，这样由 React 控制的事件处理过程 setState 不会同步更新 this.state。  
![React是怎样控制异步和同步的呢](https://upload-images.jianshu.io/upload_images/5256541-992ce78e70151b57.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/720/format/webp)

### react 生命周期

#### 挂载

- constructor()：需调用 super(props)，否则拿不到 this.props，不要在这里使用 setState()
- static getDerivedStateFromProps()：在 shouldComponentUpdate 之前调用，返回一个对象来更新 state，返回 null 则不更新，替代原来的 componentWillReceiveProps，是个静态函数，无法使用 this。
- render()：必须实现的函数
- componentDidMount()：组件挂载后调用，可在此进行订阅

#### 更新

- static getDerivedStateFromProps()
- shouldComponentUpdate()：当 props 或 state 发生变化时，在渲染前调用，若返回 false 则不会触发更新
- render()
- getSnapshotBeforeUpdate()：更新视图之前调用，可在此访问到 DOM，并将返回值返回给 componentDidUpdate。
- componentDidUpdate()：更新后调用，首次渲染不调用

#### 卸载

- componentWillUnmount()：卸载前调用，在此取消订阅

#### 错误处理

- static getDerivedStateFromError()：会在渲染阶段调用，因此不允许出现副作用
- componentDidCatch()：会在“提交”阶段被调用，因此允许执行副作用

#### render 阶段与 commit 阶段

可以理解为 render 阶段执行数据处理，筛选出需要更新的部分，commit 阶段则是将 render 阶段处理得出的数据进行 DOM 修改。

### ref

绑定：元素添加属性`ref={this.textInput}`，在`constructor`中声明`this.textInput = React.createRef()`，之后便可通过`this.textInput`访问 DOM。

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

对于方法 3 和方法 4，每次渲染这个组件的时候，都会创建不同的回调函数，更推荐方法 1 和方法 2。

## Redux

store：是一个对象，它保存整个应用的 state。可以通过`getState()`访问 state，通过`dispatch(action)`改变 state。  
action:是一个对象，描述已发生事件的普通对象，它们必须有一个`type`属性表明正在执行的 action 的类型。  
reducer:是一个纯函数，接收先前的 state 和 action，并且返回新的 state。

### 为什么要用 redux 而不使用 context

1.redex 修改 state 需要通过触发 action 修改,相对 context 直接赋值 state 来说会更规范一些也更安全一些  
2.context 无法使用 redux 的中间件

### redux 的更新流程

触发 action 后，将原先的 store 和 action 传给 reducer，经过计算后返回新的 store。  
通过`store.dispatch(action)`触发 action 后，store 会自动调用 reducer，并且传入当前 state 和 action，reducer 会返回新的 state。state 一旦有变化，就会调用监听函数`store.subscribe(function)`。如果是 react，这个时候就会触发重新渲染 View。  
https://www.cnblogs.com/goodjobluo/p/9077010.html  
http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html

### redux thunk

常规`action`只会返回一个对象，里面包含 type 值以及要修改的参数。  
在`redux`的`createStore`中使用`applyMiddleware`引入`redux-thunk`中间件后，  
允许`action`返回一个函数，这个函数接受两个参数`dispatch`和`getState`。

```javascript
//  常规action 请求开始
function AjaxStart() {
  return {
    type: AJAX_START,
  };
}
//  请求成功
function AjaxSuccess(data) {}
//  请求失败
function AjaxError(message) {}

//  异步action
function GetData() {
  return (dispath) => {
    //  请求开始前触发action修改state为请求中
    dispath(AjaxStart());
    //  发起请求
    fetch("https://www.baidu.com")
      //  请求成功 修改state为请求完成 将数据存入state
      .then((res) => dispath(AjaxSuccess(res)))
      //  请求失败 修改对应state
      .catch((error) => dispath(AjaxError(error)));
  };
}
```

https://www.jianshu.com/p/061d084d1408  
https://segmentfault.com/q/1010000013653435

# React Hook

```typescript jsx
import React, { useState, useEffect } from "react";

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
      <button onClick={() => setCount(count + 1)}>Click me</button>
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

**effect 中返回的清除函数获取的参数是上一次 effect 的参数**

**useEffect 第二个参数传入`[]`相当于`componentDidMount`**  
https://react.docschina.org/docs/hooks-overview.html

# Hook 性能优化

### React.memo

通过`React.memo`包装后的组件在相同 props 的情况下渲染相同的结果，可避免父组件更新导致子组件更新。

### useMemo

`useMemo`用于缓存某个函数的返回值，只有当依赖项改变时才会去重新执行函数。可避免函数每次渲染都进行不必要的计算。

### useCallback

props 在传递函数时传递的是函数的指针，`useCallback`可将函数缓存起来，并返回函数的指针，当依赖项没有发生改变时指针不会变化。用于避免子组件因接收父组件函数 props 而引起的不必要的刷新。

**https://juejin.cn/post/7010278471473594404**

### 如何封装全局弹窗

主要用到`ReactDOM.createRoot`和`ReactDOM.render`，往`body`里插入一个`div`后，在这个`div`里渲染弹窗组件即可。入场动画通过`animate`实现。

```typescript
class Tip {
  private static text: string | JSX.Element;
  private static icon: string | undefined;
  private static msgBox: Element;
  private static show: boolean = false;

  private static View() {
    return (
      <div
        className={`min-w-50 p-5 max-w-80 bg-white mb-80 shadow-md shadow-dark-100 text-center rounded-lg opacity-0 animate-showUp <sm:mb-0  ${
          this.icon ? "animate-duration-1800" : "animate-duration-1300"
        } `}
      >
        {
          // eslint-disable-next-line no-nested-ternary
          this.icon === "success" ? (
            <Icon icon={confirmCircle} className={"text-8xl text-green-400"} />
          ) : this.icon === "warning" ? (
            <AiOutlineWarning
              className={"text-8xl text-red-500"}
            ></AiOutlineWarning>
          ) : null
        }
        <p className="text-dark text-lg">{this.text}</p>
      </div>
    );
  }

  private static create() {
    this.msgBox = document.createElement("div");
    this.msgBox.className =
      "fixed top-0 w-full h-full left-0 flex items-center justify-center";
    document.body.appendChild(this.msgBox);
    createRoot(this.msgBox).render(this.View());
  }

  private static destruction() {
    return new Promise<void>((resolve) => {
      setTimeout(
        () => {
          this.show = false;
          document.body.removeChild(Tip.msgBox);
          resolve();
        },
        this.icon ? 1800 : 1300
      );
    });
  }

  public static open({ text, icon }: Props) {
    return new Promise<void>((resolve) => {
      if (this.show === true) return;
      this.show = true;
      this.text = text;
      this.icon = icon;
      this.create();
      this.destruction().then(resolve);
    });
  }
}

const ToolTip = (props: Props) => {
  return Tip.open(props);
};

export default ToolTip;
```

### React-Router-Dom 全局拦截器

**https://reactrouter.com/docs/en/v6/examples/auth**

### Redux 一次触发多个 action

用 redux-thunk

```javascript
/**store/index.ts***/
import { applyMiddleware, combineReducers, createStore } from "redux";
import { persistReducer, persistStore } from "redux-persist";
import storage from "redux-persist/lib/storage";
import promiseMiddleware from "redux-promise";
import thunk from "redux-thunk";

import storeData from "./reducers";

const reducers = combineReducers({
  storeData,
});

const persistConfig = {
  key: "root",
  storage,
};

const myPersistReducer = persistReducer(persistConfig, reducers);
const store: any = createStore(
  myPersistReducer,
  applyMiddleware(promiseMiddleware, thunk)
);
const persistor = persistStore(store);

export { persistor, store };

/***store/actions/index.ts***/
import initState from "../state";
import actionTypes from "./types";

export const setStoreData = (type: string, payload: any): object => {
  // @ts-ignore
  if (!actionTypes[type]) throw new Error("请传入修改的数据类型和值");
  return { type, payload };
};

export const logout =
  () => (dispatch: any, getState: any, extraArgument: any) => {
    dispatch({ type: "SET_LOGIN", payload: {} });
    dispatch({ type: "SET_CONTEXT", payload: {} });
    dispatch({ type: "SET_TOKEN", payload: null });
    dispatch({ type: "SET_BALANCE", payload: null });
    dispatch({
      type: "SET_ROUTER_HISTORY",
      payload: initState.routerHistory,
    });
    // console.log('清空完成', getState());
  };
```

### react-router-dom v6 实现面包屑

需配合`history`库

```javascript
/** index.tsx配置history ***/
import { createBrowserHistory } from 'history';
import { BrowserRouter, unstable_HistoryRouter as HistoryRouter } from 'react-router-dom';
//...

export const history = createBrowserHistory({ window });

const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement,
);

root.render(
  <Provider store={store}>
    <PersistGate loading={null} persistor={persistor}>
      <HistoryRouter history={history}>
        <App />
      </HistoryRouter>
    </PersistGate>
  </Provider>,
);


/*** Breadcrumb组件监听路由变化 ****/
import { history } from '@/index';
import RouterHistory from '@/utils/routerHistory';

const Breadcrumb:FC<BaseProps> = ({ storeData: { routerHistory } }) => {
  const location = useLocation();
  const n = useNavigate();
  const { entries, index } = routerHistory;

  const navigate = (type: string) => {
    // @ts-ignore
    const { url } = RouterHistory[type]();
    url && n(url, { state: { breadcrumb: true } });
  };

  useEffect(() => {
    RouterHistory.init();
  }, []);

  useEffect(() => {
    RouterHistory.change(location, history.action);
  }, [location.pathname]);

  // console.log('Breadcrumb', entries, index);

  return (
    <div className="flex text-3xl">
      {
        ['back', 'next'].map((e, xIndex) => (
          <BiChevronDown key={xIndex} className={`transform cursor-pointer ${xIndex === 0 ? 'rotate-90' : '-rotate-90'} ${(xIndex === 0 && index === 0) || (xIndex === 1 && index === entries.length - 1) ? 'text-gray-300 pointer-events-none' : ''}`} onClick={() => navigate(e)} />
        ))
      }
    </div>
  );
};
export default Breadcrumb;


/** routerHistory类 用于记录路由变化 **/
import { store } from '@/store';
import initState from '@/store/state';

/**
 * 路由记录类
 * 记录路由变化 用于实现面包屑
*/
export default class RouterHistory {
  //  深拷贝initState 防止造成污染
  private static routerHistory = JSON.parse(JSON.stringify(initState.routerHistory));

  private static update(entries: any, index: number) {
    try {
      this.routerHistory = { entries, index };
      store.dispatch({
        type: 'SET_ROUTER_HISTORY',
        payload: { entries, index },
      });
      return 1;
    } catch (e) {
      return 0;
    }
  }

  public static init() {
    const { storeData: { routerHistory } } = store.getState();
    this.routerHistory = routerHistory;
  }

  public static reset() {
    this.routerHistory = JSON.parse(JSON.stringify(initState.routerHistory));
  }

  public static next() {
    const { entries, index } = this.routerHistory;
    if (index !== entries.length - 1) {
      this.update(entries, index + 1);
      return entries[index + 1];
    }
    return null;
  }

  public static back() {
    const { entries, index } = this.routerHistory;
    if (entries.length !== 1 && index !== 0) {
      this.update(entries, index - 1);
      return entries[index - 1];
    }
    return null;
  }

  /**
   * @location: 当前路由信息
   * @action: 动作 PUSH/REPLACE等
   * 传入路由跳转信息 根据路由动作计算新的路由历史
  */
  public static change(location: any, action: string) {
    let { entries, index } = this.routerHistory;
    const { pathname, state } = location;
    const breadcrumb = state?.breadcrumb || false;
    const rIndex = entries.findIndex((x: any) => x.url === pathname);
    //  来源于面包屑
    if (breadcrumb) {
      return this.update(entries, index);
    //  用于页面权限拦截的场景 此时路由先push后检验权限非法 重定向到目标路由 所以需要pop掉权限不符的路由
    } if (action === 'REPLACE' && rIndex !== -1) {
      entries.pop();
      index = rIndex;
    //  正常replace => 覆盖当前index指向的路由信息即可
    } else if (action === 'REPLACE') {
      entries[index] = { url: pathname, location };
    //  路由栈中已存在目标路由 => 只需改变index
    } else if (rIndex !== -1) {
      index = rIndex;
    //  新路由 => 入栈 改变index
    } else {
      index = entries.length;
      entries.push({ url: pathname, location });
    }
    return this.update(entries, index);
  }
}
```

### React 配置环境变量

**https://www.html.cn/create-react-app/docs/adding-custom-environment-variables/**

### React 空标签带 key

**https://www.jianshu.com/p/ceae16cae6b3**

### 谷歌人机验证

**https://www.cnblogs.com/echolun/p/12436226.html**  
**https://github.com/dozoisch/react-google-recaptcha**

### 打包关闭源码显示

**https://segmentfault.com/a/1190000023053666**

### useMemo 使用 async

useMemo 无法使用 async 函数，会阻塞渲染，可改用 useState 和 useEffect。

### 实现防抖 Hook

```javascript
import { useCallback, useEffect, useRef } from "react";

const useDebounce = (callback: Function, delay: number) => {
  const { current } = useRef < any > { callback, timer: null };
  useEffect(() => {
    current.callback = callback;
  }, [callback]);

  return useCallback(
    (...args: any) => {
      if (current.timer) clearTimeout(current.timer);
      current.timer = setTimeout(() => current.callback(...args), delay);
    },
    [delay]
  );
};

export default useDebounce;
```

**https://juejin.cn/post/6854573217349107725**


## useEffect和useLayoutEffect
1. 执行时机：
     - useEffect：异步执行，在浏览器完成绘制之后执行。
     - useLayoutEffect：同步执行，在React完成DOM更新后但在浏览器绘制之前执行。
2. 用途：
     - useEffect：适用于不影响布局的副作用，例如数据获取、事件监听等。
     - useLayoutEffect：适用于需要同步执行的副作用，例如测量DOM节点大小、强制同步布局等。
  

## Mobx的store重置后无法正确刷新UI

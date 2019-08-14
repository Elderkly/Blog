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

### redux的更新流程
触发action后，将原先的store和action传给reducer，经过计算后返回新的store。   

https://www.cnblogs.com/goodjobluo/p/9077010.html


### 说说react的虚拟dom 为什么要做虚拟dom
通过babel将JSX编译成`React.createElement`的形式，通过这个方法返回的对象记录了这个DOM节点的所有信息，这个记录信息的对象我们称之为**虚拟DOM**。

### react的diff算法
为了减少Dom更新，我们需要找出渲染前后真正变化的部分，只需要更新这一部分Dom。而对比变化，找出需要更新部分的算法我们称之为**diff算法**

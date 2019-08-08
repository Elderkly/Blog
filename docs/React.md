## 说说react的虚拟dom 为什么要做虚拟dom
通过babel将JSX编译成`React.createElement`的方式，通过这个方法返回的对象记录了这个DOM节点的所有信息，这个记录信息的对象我们称之为**虚拟DOM**。

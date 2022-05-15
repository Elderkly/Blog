## 生命周期
### 初始化阶段
beforeCreate:无法访问data   
created:可以访问data   
### 挂载阶段
beforeMount:无法获取到真实dom   
mounted:可以获取真实dom   
### 更新阶段
beforeUpdate:无法获取到更新的真实dom
updated:可以获取更新的真实dom
### 销毁阶段
beforeDestroy:无法获取真实dom，可预处理data不会触发更新
destroyed:实例销毁后执行

## React和Vue的区别
* 监听数据变化的实现原理不同：vue通过`getter/setter`监听数据变化，react通过`diff`算法分辨更新的数据
* 数据流不同：vue双向数据流，react单向数据流
* 框架本质不同：vue是MVVM，react不是
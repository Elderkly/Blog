## 生命周期

### 初始化阶段

beforeCreate:无法访问 data  
created:可以访问 data

### 挂载阶段

beforeMount:无法获取到真实 dom  
mounted:可以获取真实 dom

### 更新阶段

beforeUpdate:无法获取到更新的真实 dom
updated:可以获取更新的真实 dom

### 销毁阶段

beforeDestroy:无法获取真实 dom，可预处理 data 不会触发更新
destroyed:实例销毁后执行

## React 和 Vue 的区别

- 监听数据变化的实现原理不同：vue 通过`getter/setter`监听数据变化，react 通过`diff`算法分辨更新的数据
- 数据流不同：vue 双向数据流，react 单向数据流
- 框架本质不同：vue 是 MVVM，react 不是

## Vue 部署 404

**https://zhuanlan.zhihu.com/p/525727083**

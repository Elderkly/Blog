# WebNote
笔记

##19.7.24
###浏览器渲染流程
1.处理HTML并构建DOM树  
2.处理CSS构建CSSOM树  
3.将DOM与CSSOM合并成一个渲染树  
4.根据渲染书来布局,计算每个节点的位置  
5.调用GPU绘制,合并图层,显示在屏幕上  
   
###阻塞渲染
在构建CSSOM树时，会阻塞渲染，直到CSSOM树构建完毕。  
当HTML解析到script标签时，会暂停构建DOM，直到解析完后才会在暂停的地方重新开始。  
并且CSS也会影响JS的执行，只有解析完样式表才会去执行JS，所以也可以认为这种情况下，CSS也会暂停构建DOM。  

head内的js脚本会阻塞body标签的渲染，把js脚本放在head内需要等到js执行完毕后才会渲染body。  
而将样式文件写在head中是为了避免页面元素由于样式缺失造成的白屏现象。

**https://yuchengkai.cn/docs/frontend/browser.html#%E6%B8%B2%E6%9F%93%E6%9C%BA%E5%88%B6**

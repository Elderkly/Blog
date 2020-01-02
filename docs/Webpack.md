## webpack开发工具
**1.webpack Watch     
2.webpack-dev-server     
3.webpack-dev-middleware**   

1.webpack Watch 观察模式   
能够实时监听文件修改，并重新编译模块，缺点是编译后需要自己刷新浏览器才能看到效果。   
2.webpack-dev-server   
`webpack-dev-server`为你提供了一个简单的 web 服务器，并且能够实时重新加载(live reloading)。    
3.webpack-dev-middleware     
`webpack-dev-middleware` 是一个容器(wrapper)，它可以把 webpack 处理后的文件传递给一个服务器(server)。 `webpack-dev-server` 在内部使用了它，同时，它也可以作为一个单独的包来使用，以便进行更多自定义设置来实现更多的需求。    

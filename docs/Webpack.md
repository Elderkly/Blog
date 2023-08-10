## webpack 开发工具

**1.webpack Watch  
2.webpack-dev-server  
3.webpack-dev-middleware**

1.webpack Watch 观察模式  
能够实时监听文件修改，并重新编译模块，缺点是编译后需要自己刷新浏览器才能看到效果。  
2.webpack-dev-server  
`webpack-dev-server`为你提供了一个简单的 web 服务器，并且能够实时重新加载(live reloading)。  
3.webpack-dev-middleware  
`webpack-dev-middleware` 是一个容器(wrapper)，它可以把 webpack 处理后的文件传递给一个服务器(server)。 `webpack-dev-server` 在内部使用了它，同时，它也可以作为一个单独的包来使用，以便进行更多自定义设置来实现更多的需求。

## 环境变量

技术上讲，NODE_ENV 是一个由 Node.js 暴露给执行脚本的系统环境变量。通常用于决定在开发环境与生产环境(dev-vs-prod)下，server tools(服务期工具)、build scripts(构建脚本) 和 client-side libraries(客户端库) 的行为。然而，与预期相反，在构建脚本 webpack.config.js 中 process.env.NODE_ENV 并没有被设置为 "production"，请查看 [#2537](https://github.com/webpack/webpack/issues/2537)。因此，在 webpack 配置文件中，process.env.NODE_ENV === 'production' ? '[name].[contenthash].bundle.js' : '[name].bundle.js' 这样的条件语句，无法按照预期运行。

**https://www.webpackjs.com/guides/environment-variables/**
**https://www.webpackjs.com/guides/production/**

## 安全策略

**https://www.webpackjs.com/guides/csp/**

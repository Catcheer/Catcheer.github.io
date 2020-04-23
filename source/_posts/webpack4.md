---
title: webpack4
---



#### splitChunks

提取公共依赖，生成一个单独的模块
  ```javascript
    optimization: {
           splitChunks: {
             chunks: 'all'
           }
         },
  ```


  #### filename与chunkFilename 区别
  filename 针对entry 内的文件

  ```javascript
    entry:{
        index:'./src/index.js',
    },
  ```

  打包出来就是index.js

  chunkFilename 是未被列在entry中，却又需要被打包出来的文件命名配置。比如按需加载，这样的文件是没有被列在entry中的，如使用CommonJS的方式异步加载模块：

  ```javascript
    require.ensure(["modules/tips.jsx"], function(require) {
          var a = require("modules/tips.jsx");
          // ...
      }, 'tips');
  ```

异步加载的模块是要以文件形式加载哦，所以这时生成的文件名是以chunkname配置的，生成出的文件名就是tips.min.js。
（require.ensure() API的第三个参数是给这个模块命名，否则 chunkFilename: "[name].min.js" 中的 [name] 是一个自动分配的、可读性很差的id，



#### Bundle Analysis 

  方法一：webpack --profile --json > stats.json

  方法二：webpack-bundle-analyzer

  ```javascript
    npm install --save-dev webpack-bundle-analyzer

    const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

    module.exports = {
      plugins: [
        new BundleAnalyzerPlugin()
      ]
    }
  ```



  #### htmlLoader 

  html 引用静态资源，webpack处理此静态资源用html-loader,
  ```html
  <img src='xx.png' />
  ```






  #### webpackPrefetch webpackPreload

  import(/* webpackPrefetch: true */ "...")用于预取
  <link rel =“prefetch”> 这个“资源提示”告诉浏览器这是将来某些导航可能需要的资源。

浏览器通常在处于空闲状态时获取此资源。获取资源后，在HTTP缓存中准备好以满足将来的请求。多个预取提示排队并在空闲时获取。当预取到浏览器时离开空闲状态可以取消任何正在进行的提取（并将部分响应放入缓存，继续使用Content-Range标头）并停止处理预取队列。
总结一下：空闲时取指

<link rel =“preload”>这个“资源提示”告诉浏览器这是这个导航肯定需要的资源，但稍后会被发现。Chrome在加载3秒后未使用资源时甚至会打印警告。

浏览器通常以中等优先级（不是布局阻塞）获取此资源。

总结一下：就像早先发现的一样，取正常。



#### output.path  output.publicPath devServer.contentBase devServer.publicPath
output.path 打包生成静态资源放到硬盘上的路径/目录

output.publicPath 决定引用静态资源的最终路径，最终路径=publicPath+loader路径。publicPath主要用于：例如把打包生成的包放到站点根目录下的demo目录。若不设置publicPath（默认值为'/'）则xx.com/demo路径根本找不到html和其他资源。此case 设置publicPath为'demo'或者为绝对路径即可。


devServer.contentBase 告诉服务器从哪个目录中提供内容。只有在你想要提供静态文件时才需要。devServer.publicPath 将用于确定应该从哪里提供 bundle，并且此选项优先。

默认情况下，将使用当前工作目录作为提供内容的目录，但是你可以修改为其他目录：

```javascript
  module.exports = {
  //...
  devServer: {
    contentBase: path.join(__dirname, 'public')
  }
};

```

devServer.publicPath 假设此值设置为'/public/',则相当于 将打包后的文件（其实文件是在内存中的）放到服务器的（'http://localhost:8080'）public目录下。由此可以看出：devServer.publicPath与output.publicPath设置相同。
    



#### HotModuleReplacementPlugin(HRM)
在package.json配置  会自动配置webpack.HotModuleReplacementPlugin
```javascript
 "start": "webpack-dev-server --hot"
```
或在devServer配置 
```javascript
hot:true
```
需手动配置webpack.HotModuleReplacementPlugin


#### mini-css-extract-plugin
切记 此插件仅仅适用于生产模式，并且在loaders配置项里没有'style-loader'
```
This plugin should be used only on production builds without style-loader in the loaders chain
```

#### HtmlWebpackPlugin

#### CleanWebpackPlugin

#### historyApiFallback

##### ejsLoader 


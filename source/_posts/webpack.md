---
title: webpack
date: 2020-05-28 19:14
tags: webpack
categories: 
  - 前端webpack
---
[TOC]
> 分析打包大小

```
yarn analyze
```
# 常用命令：npx webpack

# webpack 安装

> 安装本地的webpage

> webpack webpack-cli-D （-D 表示我们开发环境，上线的不需要）

# webpack 可以进行0配置

> 打包工具 -> 输出后的结果（js模块）

写好js文件，后可以通过命令：npx webpack 来运行产出结果为 dist-> main.js

> 打包（支持我们的js的模块化）


# 手动配置webpack
> 默认配置文件：webpack.config.js

> 手动使用webpack自定义文件--命令--npx webpack --config webpack.config.my.js


# 开发时--端口

> yarn add webpack-dev-server -D  --安装

> npx webpack-dev-server --启动

# 问题：每次打包的时候不能一直手动输入HTML
> 所以引入HTML插件，帮助我们把打包后的东西放入里边

> 命令： yarn add html-webpack-plugin -D



```
module: {
    // loader
    rules: [ // 规则 css-loader 解析 @improt这种语法的
    // style-loader 他是把css 插入到head的标签中
        {
            test: /\.css$/,
            use:[
                {
                    loader: 's'
                }
            ]
            
        },
        
    ]
}
```

# 打包自动加前缀（为了兼容）：autoprefixer

```
yarn add postcss-loader autoprefixer
```
# 打包压缩css插件：mini-css-extract-plugin  (将分散的打包位一个文件)
# uglifyjs-webpack-plugin
# babel 作用：将es6转化为es5
> 命令：yarn add babel-loader @babel/core @babel/preset-env -D

# 将class转化为es5 
> 命令：yarn add @babel/plugin-proposal-class-properties --dev

# 类前边的装饰器
> @babel/plugin-proposal-decorators
> @babel/plugin-proposal-class-properites

# js 中解析es6 es7语法

> 用来es6 转化 es5 语法的包：例如class 和 generator 这种高级语法的转化

```
@babel/plugin-transform-runtime
@babel/runtime

yarn add @babel/plugin-transform-runtime -D
yarn add @babel/runtime 
```
> 实例中方法的解析，例如："aaa".includes('a');

```
yarn add @babel/polyfill
```

# js 校验 工具：eslint

```
yarn add eslint eslint-loader
```


```
expose-loader 暴露 全局的loader  内联的loader
pre 前边执行的loader
normal 普通loader
内联 loader
postloader 后置
```
# Jquery :暴露全局的方法，和在每个文件加上$（有三张方法）

```
1. expose-loader 暴露到window上
2. providePlugin 给每个人提供一个$
3. 引入不打包
```
> config.js

```
let webpack = require('webpack');

new webpack.ProvidePlugin({
    $:'jquery'
})
```

# webpakck打包我们的图片

```
1.在js中创建图片来引入；
2.在css引入 background('url');
3.<img src='' alt='' />
```
> file-loader 默认会在内部生成一张图片，到build目录下,把生成的图片的名字返回回来；

```
在html中插入img标签的时候用的loader
yarn add html-withimg-loader -D
目前不关用不知道为啥
```
图片也可以转成base64这样的话就不用多发http请求，但是转成base64格式会比之前的文件大一些；
```
yarn add url-loader -D
```

```
rules:[
  {
    test: /\.(png|jpg|gif)$/,
    // 做一个限制，当我们图片小鱼多少k的时候 用base64来转化，
    // 否则用 file-loader 产生真是的图片
    use: {
      // loader: 'file-loader'
      loader: 'url-loader',
      options: {
        limit: 200*1024,
        outputPath: 'img/'
      }
       
    }
  },
]
```
给图片加上路径

```
publicPath: 'http://www.acv.com

output: {
    filename: 'index.[hash:8].js', // 打包后的文件名
    path: path.resolve(__dirname, 'build'),
    // publicPath: 'http://www.acv.com'
},

rules:[
 {
    test: /\.(png|jpg|gif)$/,
    use: {
      // loader: 'file-loader'
      loader: 'url-loader',
      options: {
        limit: 200*1024,
        outputPath: 'img/',
        publicPath: 'http:www.guoyongheng.cn' // 只给图片加路径，其他不加
      }
    }
  },
]
```

# 打包多页面应用

```
初始化： yarn init -y
安装webpack： yarn add webpack webpack-cli
```

```
module.exports = {
  // 多入口
  mode: 'development',
  entry: {
    home: './src/index.js',
    other: './src/other.js',
  },
  // entry: './src/index.js', // 入口
  output: {
    // [name] home,other
    filename: '[name].js',
    path: path.resolve(__dirname, 'dist')
  },

  plugins:[
    new HtmlWebpackPlugin({
      template: './index.html',
      filename: 'home.html',
      chunks: ['home']
    }),
    new HtmlWebpackPlugin({
      template: './index.html',
      filename: 'other.html',
      chunks: ['other']
    })
  ]
}
```

# source-map
```
yarn add @babel/core @babel/preset-env babel-loader webpack-dev-server -D
```

```
// 1) 源码映射 会单独生成一个sourcemap 文件 出错了 会标识当前报错的列和行   大  全
devtool: 'source-map'
// 增加映射文件 可以帮我们调试源代码

// 2) 不会产生单独的文件，但是可以显示行和列
devtool: 'eval-source-map'

// 3） 不会产生列 但是是一个单独的映射文件
devtool: 'cheap-module-source-map'（这个用的一般不多）

// 4） 不会产生文件 集成在打包后的文件中 不会产生列
devtool: 'cheap-module-eval-source-map'

```

# watch

> 目的：用来检测代码的变化，从而自动打包

```
module.exports = {
  watch: true,
  watchOptions: { // 监控的选项
    poll: 1000, // 每秒 问我 1000次
    aggregateTimeout: 500, // 防抖 我一直输入代码
    ignored: /node_modules/  // 不需要监控
  },
}
```

# 15. webpack小插件

1. cleanWebpackPlugin
2. copyWebpackPlugin
3. bannerPlugin  内置

```
命令： yarn add clean-webpack-plugin -D

const { CleanWebpackPlugin } = require('clean-webpack-plugin')

plugins: [
    new CleanWebpackPlugin('./dist')
  ]
```

```
let CopyWebpackPlugin  =require('copy-webpack-plugin')

plugins: [
 new CopyWebpackPlugin([
      {from: './doc', to: './'}
    ])
]
```


```
let webpack = require('webpack')
plugins: [
    new webpack.BannerPlugin('make 2020/09/8')
]
```
# 15. webpack跨域问题

```
webpack题内置express，可用server.js文件配置，然后run code 
```
server.js
```
// express

let express = require('express');

let app = express();

app.get('/api/user', (req, res) => {
  res.json({name: 'HCM第一帅'})
})

app.listen(3000)
```
webpack.config.js
```
module.exports = {
  // TODO
  devServer:{
    proxy: {
      '/api': 'http://localhost:3000' //配置了一个代理
    }
  },
}
```
由于前端采用/api/user，但是后端不一定是这样，有可能是/user，这时候需要转换一下
```
  devServer: {
    // proxy: {
    //   '/api': 'http://localhost:3000' //配置了一个代理
    // }
    proxy: {
      '/api': {
        target: 'http://localhost:3000',
        pathRewrite: {
          '/api': ''
        }
      }
    }
  },
```

```
  devServer: {
    // 2) 我们前端只想单纯来模拟数据
    before(app){
      app.get('/user', (req, res) => {
        res.json({name: 'HCM第一帅--before'})
      })
      
    },
```
## 第三种方法
```
  devServer: {
    // 3) 有服务端 不用代理来处理 能不能再服务端启动webpack 端口用服务区端口
    }
```
命令： yarn add webpack-dev-middleware -D
server.js
```
// TODO   express

let express = require('express');
let app = express();
let webpack = require('webpack');

// 中间件
let middle = require('webpack-dev-middleware');
let config = require('./webpack.config');
let compiler = webpack(config);
app.use(middle(compiler));

app.get('/user', (req, res) => {
  res.json({name: 'HCM第一帅!!!'})
})

app.listen(3000)
```
上述配置写完之后
命令： node server.js

# 17.resolve //解析
```
  resolve: { //解析
    modules: [path.resolve('node_modules')], 
    alias: { // 别名，因为引入文件太长，可以配置成简单的
      bootstrap: 'bootstrap/dist/css/bootstrap.css'
    }
  },
```

```
  resolve: { //解析
    modules: [path.resolve('node_modules')], 
    mainFields: ['style', 'main'], //可以规定先找style 后找main
    // alias: { // 别名，因为引入文件太长，可以配置成简单的
    //   bootstrap: 'bootstrap/dist/css/bootstrap.css'
    // }
  },
```

```
  resolve: { //解析
    modules: [path.resolve('node_modules')], 
    // mainFields: ['style', 'main'], //可以规定先找style 后找main
    mainFiles: [],// 入口文件的名字 index.js
    // alias: { // 别名，因为引入文件太长，可以配置成简单的
    //   bootstrap: 'bootstrap/dist/css/bootstrap.css'
    // }
  },
```
有时候我们为了引入的时候不写后缀名
：比如 import './style'
```
  resolve: { //解析
    modules: [path.resolve('node_modules')], 
    extensions: ['.js','.css', '.json'], //先找js，依次类推
    // mainFields: ['style', 'main'], //可以规定先找style 后找main
    // mainFiles: [],// 入口文件的名字 index.js
    // alias: { // 别名，因为引入文件太长，可以配置成简单的
    //   bootstrap: 'bootstrap/dist/css/bootstrap.css'
    // }
  },
```
# 18.定义环境变量
```
  plugins: [
    // TODO
    new webpack.DefinePlugin({
      DEV: JSON.stringify('production'), //console.log( dev); 开发环境dev还是生产
      FLAG: 'true',
      EXPORESSION: '1+1'
    }),
  ]    
```
# 19.区分不同的环境
> 一般会写两个webpack文件：webpack.dev.js webpack.prod.js 
> webpack.base.js 由原来的webpack.config.js变化而来

webpack.dev.js
```
let { smart } = require('webpack-merge');
let base = require('./webpack.base.js');

module.exports = smart(base, {
  mode: 'development',
  devServer: {

  },
  devtool: 'source-map'
})
```
webpack.prod.js 
```
let {smart} = require('webpack-merge');
let base =require('./webpack.base.js');

module.exports = smart(base, {
  mode: 'production',
  optimization: {
    minimize: [
      
    ]
  }
})

```

安装命令： yarn add webpack-merge -D  
运行命令：npm run build -- --config webpack.dev.js
# 20 noParse
```
module: {
    noParse: /jquery/,  // 不去解析jquery中的依赖库
    exclude: /node_modules/, //
    include: path.resolve('src'),
    
}
```
用来优化webapck打包速度，
# 20 IgnorePlugin
```
  plugins: [
    new webpack.IgnorePlugin(/\.\/locale/, /moment/),
  ]
```

# dullPlugin
```
  output: {
    // [name] home,other
    filename: '_dll_[name].js',
    path: path.resolve(__dirname, 'dist'),
    library: '_dll_[name]',
    libraryTarget: 'commonjs' // var umd var this ...
  },
  
  plugins: [
    new webpack.DllPlugin({
      name: '_dll_[name].js',
      path: path.resolve(__dirname, 'dist', 'manifest,json')
    })
  ]

```
webpack.config.js
```
  
    plugins: [
    // TODO
    new webpack.DllReferencePlugin({
      manifest: path.resolve(__dirname, 'dist', 'manifest.json')
    }),
```

相关命令： npx webpack --config webpack.config.react.js

# 23.happypack
# 24.webpack自带优化
a.js
```
const b
const c
export default {b,c}
```
```
import a from './a.js'
console.log(a.b());
这时候打包只打包 b 还是c也打包呢？
答案：
//import语法 dev下都有，生产环境会过滤掉没有用到的部分
//这种模式叫 tree-shaking 把没用到的代码 自动删除掉
require 没有这种功能
//这就是前端为什么要使用import语法

// scope hosting 作用域提升
let a = 1;
let b = 2;
let c = 3;
let d =a+b+c;// 在webpack中自动省却 可以简化的代码
console.log(d,'------------')
最后出来的console是webpack自己打包出来的，而不是浏览器算出来的
```
# 25. 抽离公共代码
> 问题:如果有两个js文件，他们都同时引入了一个同一个js文件，那么这个js文件是不是要被引用两次？

```
module.exports = {
  optimization:{ // commonChunkPlugins
    splitChunks: { // 分割代码块
      cacheGroups:{ // 缓存组 cacheGroups?, 
        common: { // 公共的模块
          chunks: 'initial',
          minSize: 0,
          minChunks: 2,
        },
        vendor: {
          priority:1, // 权重 ：代表先执行 第三方模块的抽离，再对代码块抽离
          test: /node_modules/, // 把你抽离
          chunks: 'initial',
          minSize: 0,
          minChunks: 2,
        }

      }
    }
  },
}
```
# 26. 懒加载
> 用到的插件
index.js

```
// es6 草案中的语法， jsonp实现动态加载文件
import('./source.js').then(data => {
    console.log(data.default)
})


```
yarn add @babel/plugin-syntax-dynamic-import -D
然后在相应webpack.config.js上 

```
plugins: [
    '@babel/plugin-syntax-dynamic-import'
]
```
# 27. 热更新
其实是增量刷新，页面没有刷新打圈圈
```
  devServer: {
    hot: true, //TODO 启用热更新
    port: 3000,
    open: true,
    contentBase: './dist',
  },
  new webpack.NamedModulesPlugin(),//todo 打印更新的模块路径
    new webpack.HotModuleReplacementPlugin() //todo 热更新插件
```
# 28. tapable
yarn add tapale
> 这个其中用到了发布订阅，很重要

```
// TODO 订阅发布 
class SyncHook {  // 同步钩子
  constructor(args){ // args => ['name']
    this.tasks = [];
  }
  tap(name, task){
    this.tasks.push(task);
  }
  call(...args){
    this.tasks.forEach(task => task(...args));
  }
}

let hook = new SyncHook(['name']);
hook.tap('react', function name(name) {
  console.log('react: ', name);
  
})
hook.tap('node', function name(name) {
  console.log('node: ', name);
  
})
hook.call('jw');
```
> tap向数组中追加方法，call调用数组中的方法

# 29. tapable
> // 同步保险钩子:SyncBailHook

```
class SyncBailHook {  
  constructor(args){ // args => ['name']
    this.tasks = [];
  }
  tap(name, task){
    this.tasks.push(task);
  }
  call(...args){
    let ret; // 当前这个函数的返回
    let index = 0; //当前要执行第一个
    while (ret === undefined && index < this.tasks.length) {
      ret = this.tasks[index++](...args)
    }
  }
}

let hook = new SyncBailHook(['name']);
hook.tap('react', function name(name) {
  console.log('react: ', name);
  return '停止乡下运行'
})
hook.tap('node', function name(name) {
  console.log('node: ', name);
  
})
hook.call('jw');
```
> waterfall 瀑布

```
// TODO 订阅发布
class SyncWaterfallHook {
  constructor(args){ // args => ['name']
    this.tasks = [];
  }
  tap(name, task){
    this.tasks.push(task);
  }
  call(...args){
    let [first, ...others] = this.tasks;
    let  ret = first(...args);
    others.reduce((a,b) => {
      return b(a)
    }, ret)
  }
}

let hook = new SyncWaterfallHook(['name']);
hook.tap('react', function name(name) {
  console.log('react: ', name);
  return 'reactOK'
})
hook.tap('node', function name(data) {
  console.log('node: ', data);
  return 'NODEok'
})
hook.tap('Webpack', function name(data) {
  console.log('data: ', data);
  
})
hook.call('jw');
```

> 同步遇到某个不反悔undefined的监听函数会多次执行

```
// TODO 订阅发布
class SyncLoopllHook {
  constructor(args){ // args => ['name']
    this.tasks = [];
  }
  tap(name, task){
    this.tasks.push(task);
  }
  call(...args){
    this.tasks.forEach(task => {
      let ret;
      do {
        ret = task(...args)
      } while (ret != undefined);
    })
  }
}

let hook = new SyncLoopllHook(['name']);
let num = 0
hook.tap('react', function name(name) {
  console.log('react: ', name);
  return  ++num === 3? undefined:'继续学'
})
hook.tap('node', function name(name) {
  console.log('node: ', name);
})
hook.tap('Webpack', function name(name) {
  console.log('Webpack: ', name);
})
hook.call('jw');
```
# 30. AsyncHooks
> 异步钩子

```
// TODO 订阅发布 异步AsyncLoopllHook
class AsyncLoopllHook {
  constructor(args){ // args => ['name']
    this.tasks = [];
  }
  tapAsync(name, task){
    this.tasks.push(task);
  }
  callAsync(...args){
    let finalCallback = args.pop(); //拿出最终的函数
    let index = 0;
    let done = () => {
      index++;
      if (index == this.tasks.length) {
        finalCallback();
      }
    }
    this.tasks.forEach(task => {
      task(...args, done);
    })
  }
}

let hook = new AsyncLoopllHook(['name']);
let total = 0
hook.tapAsync('react', function name(name,cb) {
  setTimeout(() =>{
    console.log('react', name)
    cb();
  }, 1000)
})
hook.tapAsync('node', function name(name, cb) {
  setTimeout(() =>{
    console.log('react', name)
    cb();
  }, 1000)
})
hook.callAsync('jw', function (params) {
  console.log('end');
});
```
>  tapable库中有三种注册方法
tap
tapAsync
tapPromise
// 调用 三种方法 call callAsync promise

异步并发

```
// TODO 订阅发布 异步AsyncLoopllHook
class AsyncLoopllHook {
  constructor(args){ // args => ['name']
    this.tasks = [];
  }
  tapPromise(name, task){
    this.tasks.push(task);
  }
  promise(...args){
    let tasks = this.tasks.map(task => task(...args));
    return Promise.all(tasks);
    // return Promise.all(this.tasks);
  }
}

let hook = new AsyncLoopllHook(['name']);
let total = 0
hook.tapPromise('react', function name(name) {
  return new Promise((resolve, reject) => {
    setTimeout(() =>{
      console.log('react', name);
      resolve();
    }, 1000)
  })

})
hook.tapPromise('node', function name(name) {
  return new Promise((resolve, reject) => {
    setTimeout(() =>{
      console.log('node', name);
      resolve();
    }, 1000)
  })
})
hook.promise('jw').then(function(){
  console.log('end')
})
```
# 31. 异步串行
> AsyncSeriesHook

```
// TODO 订阅发布 异步串行 AsyncSeries
class AsyncSeries {
  constructor(args) { // args => ['name']
    this.tasks = [];
  }
  tapAsync(name, task) {
    this.tasks.push(task);
  }
  callAsync(...args) {
    console.log('args: ', args);
    let finalCallback = args.pop();
    let index = 0;
    let next = () => {
      if (this.tasks.length === index) {
        return finalCallback();
      }
      let task = this.tasks[index++];
      task(...args, next);
    }
    next();
  }
}

let hook = new AsyncSeries(['name']);
let total = 0

hook.tapAsync('react', function name(name, cb) {
  setTimeout(() => {
    console.log('react', name);
    cb();
  }, 1000)

})

hook.tapAsync('node', function name(name, cb) {
  setTimeout(() => {
    console.log('node', name);
    cb();
  }, 1000)
})

hook.callAsync('HCM第一帅', function () {
  console.log('end')
})
```
>  异步串行 AsyncSeries

```
// TODO 订阅发布 异步串行 AsyncSeries
class AsyncSeries {
  constructor(args) { // args => ['name']
    this.tasks = [];
  }
  tapPromise(name, task) {
    this.tasks.push(task);
  }
  promise(...args) {
    let [first, ...others] = this.tasks;
    return others.reduce((a, b) => { // redux 源码
      return a.then(() => b(...args))
    }, first(...args))
  }
}

let hook = new AsyncSeries(['name']);
let total = 0

hook.tapPromise('react', function name(name) {
  return new Promise((rolove, reject) => {
    setTimeout(() => {
      console.log('react', name);
      rolove();
    }, 1000)
  })
})

hook.tapPromise('node', function name(name) {
  return new Promise((rolove, reject) => {
    setTimeout(() => {
      console.log('node', name);
      rolove();
    }, 1000)
  })
})

hook.promise('HCM第一帅').then(function () {
  console.log('end')
})
```
# 32.异步串行钩子函数

```

```
# 35.
```

```
# 36.

```

```
# 41 loader 配置
> loader的分类，pre 在前边 post 在后面， normal
> loader的顺序， pre + normal +inline + post

```
从右向左，从下到上

```
# 42
> babel-loader的实现 

```
@babel/preset-env 高级语法转换成低级语法
```
# loader作用

```
css-loader 是用来解析里边的css里边的一些，比如：

@color: red;

body{
    background: @color;
    
    background: url('./public.jpg')
}
```














































---
description: "webpack已经复杂到需要这么厚的一本书来讲了吗，前端还真是不容易呢（逃     \t\t\t\t\t————————知乎某网友"
---

# Webpacck Configuration Engineer



### Webpack 基础模块

* Loader 用于加载处理各种文件

```
rules: [ 
	{
	   	 test : /\ . css$/ ,
		 use : [’ style-loade r ’,’css-loader?minimize’ ],
	 }
]
```

use : \[’ babel-loader?cacheDirectory ’]这种处理程序后？再接参数的形式表示传给这个处理程序的参数此例为缓存babel的编译结果以加快重新编译的速度，需要多个参数传入的时候可以添加一个Options：{} 来进行描述 使用perser属性可以更细粒度的配置那些模块的语法被解析那些不解析。

* Plugin 用于扩展Webpack功能

```
canst ExtractTextPlugin =require (’ extract-text-webpack-plugin ’) ;
// 其他配置
plugins: [ 
	new ExtractTextPlugin({
		filename [contenthash:8].css`,
	}),
]
```

* noParse配置项可以让Webpack忽略对部分没有采用模块化的文件的递归解析和处理，提高构建性能

```
noParse: /jquery|chartjs/
noParse : (content)=> { 
	return /jqery|chart.js/.test(conte); 
}
```

### resolve相关配置

* alias 配置项设置路径别名

```
reaolve:{
	alias: {
		components: './src/components'
		//'text$'则表明只有以text结尾的语句才会被识别为别名替代
	}
}
```

* mainFields配置决定优先采用同一个模块针对不同第三方环境提供的不同代码中的那一个。
* descriptionFiles设置描述第三方模块的文件名称（一般都为Webpack.json）。
* enforceExtension配置被改为true时则所有导入语句都必须带后缀

### DevServer相关配置

* devServer.hot是否使用热更新
* devServer.inline用于配置是否将这个代理客户端自动注入将运行在页面中的Chunk里，如果想使用热更新最好开启此功能
* historyApiFallback用于方便单页面应用开发使访问子路由时都返回index.html，如果项目是多个单页面应用组成的就要对其进行特殊配置
* contentBase配置DevServerHTTP服务器的文件根目录。特殊情况下以 contentBase : path.join(\_dirname,’pubilc’) 的形式添加额外文件
* haeders配置HTTP响应头
* host 配置DevServer服务监听的地址
* port配置DevServer服务监听的端口
* allowedHosts配置一个白名单列表，只有HTTP请求的HOST在列表里才能正常的返回
* disableHostCheck配置用于配置是否关闭用于DNS重新绑定的HTTP请求的HOST检查。DevServer默认只接受来自本地的请求，关闭后可以接受来自任意HOST的请求。他通常用于搭配—host 0.0.0.0使用，因为想让其他设备访问自己的本地服务，但访问时是直接通过IP地址访问而不是通过HOST访问，所以要 关闭HOST检查。
* HTTPS DevServer默认使用HTTP服务，当我们需要HTTPS的时候配置此项
* Open用于指定DevServer完成构建后系统默认浏览器打开的网页

### 其他配置项

* Devtool用于配置如何生成SourceMap（帮助压缩后代码定位其初始位置）
* target配置项可以让Webpack构建出针对不同运行环境的代码
* Watch和WatchOption 监听文件更新和配置更详细的监听机制
* Externals用来告诉Webpack在构建代码时不打包那些模块
* ResolveLoader用来告诉Webpack如何去寻找Loader

### 整体配置结构

```
const path = require('path')
moudle.exports = {
  //entry表示入口，Webpack执行构建的第一步将从Entry开始，可抽象成输入
  //类型可以是String,object,array
  entry: './app/entry', //只有一个入口，入口文件只有一个
  entry: ['./app/entry1', './app/entry2'], //只有一个入口，入口文件有两个
  entry: { //有两个入口
    a: './app/entry-a',
    b: ['./app/entry-b1','./app/entry-b2']
  },
  output: {
    path: path.resolve(__dirname, 'dist'), //输出文件存放的目录，必须是string类型的绝对路径
    filename: 'finish.js',//完整的名称
    filename: '[name].js',//在配置了多个entry时，通过名称模板为不同的entry生成不同的文件名称
    filename: '[chunkhash].js',//根据文件内容的Hash值生成文件的名称，用于浏览器长时间缓存文件
    //发布到线上的所有资源的URL前缀，为String类型
    pubilcPath: '/assets/',//放到指定目录下
    publicPath: '', //放到根目录下
    publicPath: 'https://cdn.example.com',//放到CDN下
    library: 'MyLibrary', //导出库的名称，String类型，不设置时，默认的输出格式是匿名的立即执行函数
    libraryTarget: 'umd', //到出库的类型，为枚举类型，默认是var,可选项有umd umd2 commonjs2 commonj amd this var assign window global jsonp
    pathinfo: true,//是否包含有用的文件路径信息到生成的代码里
    chunkFilename: '[id].js',//附加Chunk的文件名称
    chunkFilename: '[chunkhash].js',
    jsonpFunction: 'myWebpackJsonp',//JSONP异步加载资源时的回调函数名称，需要和服务器端搭配使用
    sourceMaoFilename: '[file].map',//生成的Source Map文件的名称
    devtoolMoudleFulenameTemplate: 'webpack:///[resource-payh]',//浏览器开发者工具里显示的源码模块名称
    //异步加载跨域的资源时使用的方式
    crossOriginLoading: 'use-credentials',
    crossOriginLoading: 'anonymous',
    crossOriginLoading: false,
  },
  moudle:{//模块配置相关
    rules: [ //配置Loader
      {
        test: /\.jsx?$/, //正则匹配命中要使用的Loader文件
        include:[//只会匹配这里的文件
          path.resolve(__dirname, 'app')
        ],
        exclude:[//忽略这里的文件
          path.resolve(__dirname, 'app/demo-files')
        ],
        use:[ //使用那些Loader，有先后次序。从后向前执行
          'style-loader',//直接使用Loader的名称
          {
            loader: 'css-loader',
            options:{// 向html-loader传一些参数
            }
          }
        ]
      },
    ],
    noParse:[ // 不用解析和处理的模块
      /special-library\.js$/ //用正则匹配
    ],
  },
  plugins:[],//插件配置
  resolve: {
    moudle:[ // 寻找模块的根目录，为array类型，默认以node_moudles为根目录
      'node_modules',
      path.resolve(__dirname,'app')
    ],
    extensions: ['.js', '.json', '.jsx', '.css'],//模块后缀名
    alias: {// 模块别名配置，用于映射模块
      'moudle': 'new-module',
      'only-module$': 'new-module',// 只匹配以only-module结尾的
    },
    alias: [
      {
        name: 'module',//老模块
        alias: 'new-moudle',//新模块
        onlyModule: true,//为真时则只有'nodule'会被映射，否则'moudle/inner/path'也会被映射
      }
    ],
    symlinks: true,//是否跟随文件的软连接去搜寻模块的路径
    descriptionFiles: ['pack.json'],//模块信息描述文件
    mainFields: ['main'],//模块的描述文件里描述入口文件的字段名
    enforceExtension: false,//是否强制导入语句写明文件后缀
  },
  //输出文件的性能检查配置
  performance:{
    hints: 'warning', //有性能问题时输出警告
    hits: 'error', //有性能问题时输出错误
    hints: false, //关闭性能检查
    maxAssetSize: 200000,//最大文件大小（单位bytes）
    maxEntrypointSize: 400000,//最大入口文件大小（单位bytes）
    assetFilter: function(assetFilename){ // 过滤要检查的文件
      return assetFilename.endsWith('.css') || assetFilename.endsWith('.js');
    }
  },
  devtool: 'source-map',//配置source-map类型
  conterxt:__dirname,//Webpack使用的根目录，string类型必须是绝对路径
  //配置输出代码的运行环境
  target:'web',//浏览器，默认
  target:'webworker',//webworker
  target:'node',//Node.js使用require语句加载Chunk代码
  target:'async-node',//Node.js,异步加载Cunk代码
  target:'node-webkit',//nw.js
  target:'electron-main',//electron,主线程
  target:'electron-renderer',//electron,渲染线程
  externals:{// 使用来自JavaScript运行环境的全局变量
    jquery:'jQuery'
  },
  stats:{ //控制台输出日志控制
    assets:true,
    colors:true,
      errors:true,
      errorDetails:true,
      hash:true,
  },
  devServer:{//DevServer相关配置
    proxy: {//代理到后端服务接口
      '/api':'http://localhost:3000'
    },
    contentBase: path.join(__dirname, 'public'),//配置DevServer HTTP 服务器的文件根目录
    compress: true,//是否开启Gzip压缩
    historyApiFallback: true,//是否开发HTML5 History API网页（单页面应用）
    hot: true,//是否开启热更新
    https: false,//是否开启https
  },
  profile:true,//是否捕捉Webpack构建的性能信息
  cache:false,//是否启用缓存
  watch: true,//是否开始
  watchOptions: {//监听模式选项
    ignored: /node_modules/,  //不监听的文件或文件夹，支持正则匹配。默认为空
    aggregateTimeout:300,//监听到变化后，等300ms在执行动作
    poll:1000,//每秒的查询次数
  },
}
```

### 主要功能

* 压缩混淆代码 uglify.js
* JS兼容性补丁 polyfill.js babel.js
* 处理依赖

### webpack output

1. Assets
2. Chunk
3. moudle 包

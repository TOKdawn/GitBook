---
description: 关于webpack,Vite,rollup,与Node
---

# 构建工具与更多



Rollup 轻量级

#### 基础配置和命令

```
//rollup.config.js
import resolve from ‘rollup-plugin-node-resolve’;
import babel from ‘rollup-plugin-babel’;
import $ from ‘jquery’;


export default {
  input: ‘./main.js’,//入口文件
  output:{
    file:’bundle.js’,//出口文件
    format: ‘cjs’,// CommonJS规范
	  name: ‘MyBundle’

  },
	plugins: [ 
		json(), //JSON支持,需要rollup-plugin-json
		resolve(),//模块引用
		babel({
      	exclude: ‘node_modules/**’ // 过滤node_modules
    	})
	],
 	globals: { //全局组件
    jquery: ‘$’
  },
 banner: ‘/* my-library version ‘ + version + ‘ */‘,//头尾附加
 footer: ‘/* follow me on Twitter! @rich_harris */‘
 external:[‘lodash’]//外部引用
}



//.babelrc
{
  “presets”: [
    [“latest”, {
      “es2015”: {
        “modules”: false
      }
    }]
  ],
  “plugins”: [“external-helpers”],
}

```

* `rollup -c` 以配置执行打包
* `rollup src/main.js -o bundle.js -f cjs` 把main.js 打包成bundle.js 采用CommonJS规范
* rollup-plugin-node-resolve 此插件可以告诉 Rollup 如何查找外部模块。
* rollup-plugin-commonjs辅助导出ES6模块
* 格式( format -f/—output.format )String 生成包的格式。 下列之一:&#x20;

amd – 异步模块定义，用于像RequireJS这样的模块加载器&#x20;

cjs – CommonJS，适用于 Node 和 Browserify/Webpack&#x20;

esm – 将软件包保存为 ES 模块文件，在现代浏览器中可以通过 标签引入&#x20;

iife – 一个自动执行的功能，适合作为标签。如果要为应用程序创建一个捆绑包，您可能想要使用它，因为它会使文件大小变小。&#x20;

umd – 通用模块定义，以amd，cjs 和 iife 为一体&#x20;

system - SystemJS 加载器格式

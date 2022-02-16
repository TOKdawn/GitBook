---
description: 通过JSX构建自定义组件
---

# JSX与自定义组件

为了防止B端冗余项目,为了应对企业微信的隐私规则,贯彻一个项目多端复用,可爱的JSX站出来啦!!

### 前期准备

```
// 相关依赖
"babel-helper-vue-jsx-merge-props": "^2.0.3",
"babel-plugin-syntax-jsx": "^6.18.0",
"babel-plugin-transform-vue-jsx": "^3.7.0",
"babel-preset-env": "^1.7.0",

 babel 配置文件[.babelrc] 内 plugins 加入 transform-vue-jsx支持
{
  "presets": [
    [
      "@babel/preset-env" , {
        "modules": false, // 默认都是支持 CommonJS
        "targets": {
          "chrome": "38",
          "ie": "10"
        }
      }
    ]
  ],
  "comments": false, //不产生注释
  "plugins": [
    "@babel/plugin-transform-runtime",
    "transform-vue-jsx"   <-------此处,(用不用写这么详细啊,喂!
  ]
}


```

### 组件

```
// JSX组件
//核心组件代码 name.vue
<script>
  export default {
    name: 'DSName', //组件名
    props: {
        color: String,
        size: String
      },
    computed: {
      tagSize() {
        return this.size || '14px' // 默认样式
      },
      weixinFlag(){//判定当前项目环境
        return this.$store.state.webType && this.$store.state.webType == 'WEIXIN' ? true : false 
      }
    },
    render(h) {//核心渲染方法
      const nameDOM = this.weixinFlag ?  (<span  style={{color: this.color,fontSize: this.size}}>
         <ww-open-data type="userName" openid={this.$slots.default[0].text}></ww-open-data>//企业微信环境
      </span>) : (<span  style={{color: this.color,fontSize: this.size}}>
        { this.$slots.default[0].text }//常规渲染
      </span>)
      return nameDOM 
    }
  };
</script>

//main.js内全局引入
import DSName from './src/name';
Vue.component(DSName.name, DSName);

```

因为企业微信的安全策略,名称渲染都要用它们提供的组件,以JSX的方式写这种特殊DOM结构代码更清真一些.整个组件解决了不同B端环境下名称的渲染,而且使用slots的方式在实际调用的时候也足够方便.正常环境传userName,企业微信就是openID

```
// Some code
  <DSName>测试姓名</DSName>
  <DSName size="16px" color="#001100">测试姓名</DSName>

```

### 后记

&#x20;`this.$slots.default[0].text` 可以用于获取默认插槽分发进来的内容值

实际上真的要解决企业微信的这种名称渲染问题还得在企业微信项目里做整体的组件初始化,以及在做完名称渲染后调用

&#x20;`window.WWOpenData.bindAll(document.querySelectorAll('ww-open-data'))`

不加可能会在一些情况下出问题,但是这个方法我没写入到组件中,感觉会一直重复调用很蠢,需要在实际的vue页面的生命周期内加入

这只是JSX组件的一个很简单示范,更多操作请查阅JSX文档

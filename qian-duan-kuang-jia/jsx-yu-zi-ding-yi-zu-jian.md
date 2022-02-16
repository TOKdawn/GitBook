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
      weixinFlag(){//依据当前项目环境判断 da
        return this.$store.state.webType && this.$store.state.webType == 'WEIXIN' ? true : false 
      }
    },
    render(h) {
      const nameDOM = this.weixinFlag ? (<span class={classes} style={{color: this.color,fontSize: this.size}}>
        { this.$slots.default }
      </span>) : (<span class={classes} style={{color: this.color,fontSize: this.size}}>
         <ww-open-data type="userName" openid={this.$slots.default}></ww-open-data>
      </span>)
      return nameDOM 
    }
  };
</script>

```

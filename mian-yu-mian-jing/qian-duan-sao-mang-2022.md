# 前端扫盲2022



#### CSS

* 清除浮动方式: 父元素单独定义高度,父级定义overflow:hidden,浮动元素后加空标签clear:both; height:0;overFlow:hidden,
* Relative 和 Static 二者都不脱离文档流,但是relative可以设置left right 产生于原位置相对的位移,默认值static则不行
* 垂直居中: 行内元素 vertical-align：middle; 父元素定高 `transform:translateY（-50%` 父元素不定高`top:50%,transform;translateY（-50%)` ;弹性盒模型flex
* 块级元素：div h1-h6 hr p ul ol table address blockquote dir frommenu 行内元素：a br I em img input select span sub sup u textarea 可变元素：button del iframe ins
* Flex-direction：弹性容器中子元素排列方式（主轴排列方式） Flex-wrap：设置弹性盒子的子元素超出父容器时是否换行 Flex-flow：是 flex-direction 和 flex-wrap 简写形式 Align-item：设置弹性盒子元素在侧轴上的对齐方式 Align-content：设置行对齐 Justify-content：设置弹性盒子元素在主轴上的对齐方



### 后端

* JWT与session区别:session需要后端进行存储,登陆后服务端生成标识存在数据库中,并且返回给前端进行Cookie存储,之后前端请求都会带着Cookie信息,后端接收后和服务器内数据进行比对鉴权,注销时前后端都销毁; JWT不需要后端存储,登陆后服务端生成token反给前端,前端存在LocalStorage中,之后请求都会带上token,后端解析token鉴权,无效返401,用户注销时前端直接销毁token.
* JWT如何踢人:

---
description: 是VDOM,不是VTuber
---

# 虚拟dom



HTML文件最终都要以DOM的形式进行解析,而Virtual DOM本质上是通过JS对实际DOM进行一层抽象,因为直接操作DOM性能不够且复杂,所以把设计一种方法论,可以通过JS对象的形式描述实际DOM,进而快捷高效的操作V DOM,最后会通过特定的转换方式吧VDOM转换为实际DOM再进行更改. 比如一组实际DOM

```
<ul id=‘list’>
  <li class=‘item’>itemA</li>
  <li class=‘item’>itemB</li>
</ul>
```

以虚拟DOM的方式表达就是这样的:

```
{
	tag:’ul’, // 元素的标签类型 
	attrs:{// 表示指定元素身上的属性 
		id:’list’
	 }, 
	children:[ // ul元素的子节点 
		{ 
			tag: ‘li’, 
			attrs:{ 
				className:’item’ 
			}, 
			children:[‘itemA’] 
		}, { 
			tag: ‘li’, 
			attrs:{
				 className:’item’ 
			}, 
			children:[‘itemB’]
		} ] 
}
tag: 指定元素的标签类型，案例为：'ul’ （react中用type）
attrs: 表示指定元素身上的属性，如id,class, style, 自定义属性等（react中用props）
Children: 表示指定元素是否有子节点，参数以数组的形式传入,如果是文本就是数组中为字符串
```

#### VDOM的好处

* 首先肯定是性能优化,操作DOM非常耗时的操作,频繁的变动DOM会造成浏览器的回流重绘,所以把DOM进行抽象后,可以再patch的过程中尽可能一次性的更新DOM,保证性能
* 便捷,直接操作DOM必须通过相关的API不方便,而抽象后的DOM形制上其实是JS对象,可以更方便的操作,提高编码效率.
* 更好的跨平台性,比如Node.js就没有DOM,如果想实现SSR(服务端渲染),那么一个方式就是借助Virtual DOM,因为Virtual DOM本身是JavaScript对象. 而且在的ReactNative，React VR、weex都是使用了虚拟dom。

#### 缺点

* VDOM到实际DOM的过程实际上是需要进行一层\[翻译]的,所以首次渲染大量DOM时,性能不如原生DOM,一次性插入大量DOM的情况下也不如直接innerHTML
* 如果你的场景是虚拟 DOM 大量更改，这是合适的。但是单一的，频繁的更新的话，虚拟 DOM 将会花费更多的时间处理计算的工作。比如，你有一个 DOM 节点相对较少页面，用虚拟 DOM，它实际上有可能会更慢。但对于大多数单页面应用，这应该都会更快。这也是为啥react和vue中的更新用了异步的方法，频繁更新时，只更新最后一次的。

### 虚拟DOM的实现案例

实现一套VDOM系统有三个核心问题,把大象装冰箱分三步,开冰箱,装大象,关冰箱(逃

* complie:把DOM转换为VDOM
* diff:通过算法,计算旧DOM结构和新DOM结构间的差异
* patch:吧VDOM转换为DOM

### 处理属性

主要包括几类的处理

1. class:这里我们可以理解为动态的类名，sel上的类可以理解为静态的，例如上面class:{active:true}我们可以通过控制这个变量来表示此元素是否是当前被点击
2. style:内联样式
3. on:绑定的事件类型
4. dataset:data属性
5. hook:钩子函数

```
vnode = h('div#divId.red', {
    'class': {
        'active': true
    },
    'style': {
        'color': 'red'
    },
    'on': {
        'click': clickFn
    }    
}, [h('p', {}, '文本内容')])
function clickFn() {
    console.log(click')
}
vnode = patch(app, vnode);
```

---
description: 在路由切换中添加逻辑处理
---

# 路由管理

#### 整体代码

```
// Some code
const { location, history } = window

const createHistoryStateStore = (state = location.href)=>{
  const getState = ()=>{
    if (listenerSet.size === 0) throw new Error('should subscribe before getState')
    return state
  }
  const setState = (nextState, shouldPushState) =>{ //新路由和状态
    if (typeof (nextState) !== 'string') throw new Error(`non-string href: ${nextState}`) 
    if (nextState === state) return state //新路由和当前路由一样 不做处理
    console.log('do set nextState:',nextState, 'shouldPushState:',shouldPushState,'state:',state)
    const prevState = state //把当前存储的路由状态当做旧路由
    state = nextState//传入的新路由为当前路由状态
    shouldPushState !== 'skip' && history.pushState(null, '', state) //判断操作性质,决定是否需要手动更新histroy栈
    listenerSet.forEach((listener) => listener(state, prevState)) //执行路由变更处理逻辑
    return state
  }
  const listenerPopState = () => { setState(location.href, 'skip') } //原生浏览器操作时才触发
  const listenerHashChange = () => { setState(location.href) } // js调用history.pushState()或history.replaceState() 或者直接改地址框地址 记录
  const listenerSet = new Set() //存储路由变更时执行逻辑的函数集合
  const subscribe = (listener) => { //绑定事件监听
    listenerSet.add(listener) //存储处理函数
    if (listenerSet.size !== 1) return //如果以前已经添加过监听处理,无需重复绑定
    window.addEventListener('popstate', listenerPopState) //原生流量器操作
    window.addEventListener('hashchange', listenerHashChange) // js操作
  }
  const unsubscribe = (listener) => {
    listenerSet.delete(listener)// 删除存储处理函数 
    if (listenerSet.size !== 0) return //如果删除某个处理函数后仍存在其他处理逻辑则不需解绑
    window.removeEventListener('popstate', listenerPopState) 
    window.removeEventListener('hashchange', listenerHashChange)
  }

  return { getState, setState, subscribe, unsubscribe }
}
export { createHistoryStateStore } 


// e.g:

var his = createHistoryStateStore()
his.subscribe((state, prevState)=>{ console.log('state:',state, 'prevState:',prevState)})
```

* 用 listenerSet做集合可以维护多套处理逻辑,对应的要注意绑定和取消监听逻辑
* 核心逻辑内始终只维护当前页面的href信息,和页面变更后传入的新页面href信息,以此提供给处理函数
* 唯一的要注意的是要不要自己进行history.pushState调用,浏览器自发处理不需要,JS调用需要
* 实际上路由栈还是靠浏览器自己维护的路由栈信息

> ## popstate
>
> 当活动历史记录条目更改时，将触发popstate事件。如果被激活的历史记录条目是通过对history.pushState（）的调用创建的，或者受到对history.replaceState（）的调用的影响，popstate事件的state属性包含历史条目的状态对象的副本。
>
> 需要注意的是调用`history.pushState()`或`history.replaceState()`不会触发`popstate`事件。只有在做出浏览器动作时，才会触发该事件，如用户点击浏览器的回退按钮（或者在Javascript代码中调用`history.back()`或者`history.forward()`方法）
>
> 不同的浏览器在加载页面时处理`popstate`事件的形式存在差异。页面加载时Chrome和Safari通常会触发(emit )`popstate`事件，但Firefox则不会

> ## History.pushState()
>
> ```
> history.pushState(state, title,[ url])
> ```
>
> #### [参数](https://developer.mozilla.org/zh-CN/docs/Web/API/History/pushState#%E5%8F%82%E6%95%B0) <a href="#can-shu" id="can-shu"></a>
>
> `state`状态对象是一个JavaScript对象，它与`pushState()`创建的新历史记录条目相关联。 每当用户导航到新状态时，都会触发[`popstate (en-US)`](https://developer.mozilla.org/en-US/docs/Web/API/Window/popstate\_event)事件，并且该事件的状态属性包含历史记录条目的状态对象的副本。状态对象可以是任何可以序列化的对象。 因为Firefox将状态对象保存到用户的磁盘上，以便用户重新启动浏览器后可以将其还原，所以我们对状态对象的序列化表示施加了640k个字符的大小限制。 如果将序列化表示形式大于此状态的状态对象传递给`pushState()`，则该方法将引发异常。 如果您需要更多空间，建议您使用 [`sessionStorage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/sessionStorage)或者[`localStorage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/localStorage)。`title`[当前大多数浏览器都忽略此参数](https://github.com/whatwg/html/issues/2174)，尽管将来可能会使用它。 在此处传递空字符串应该可以防止将来对方法的更改。 或者，您可以为要移动的状态传递简短的标题。`url` 可选新历史记录条目的URL由此参数指定。 请注意，浏览器不会在调用`pushState()` 之后尝试加载此URL，但可能会稍后尝试加载URL，例如在用户重新启动浏览器之后。 新的URL不必是绝对的。 如果是相对的，则相对于当前URL进行解析。 新网址必须与当前网址相同 [origin](https://developer.mozilla.org/zh-CN/docs/Glossary/Origin)； 否则，`pushState()`将引发异常。 如果未指定此参数，则将其设置为文档的当前URL。




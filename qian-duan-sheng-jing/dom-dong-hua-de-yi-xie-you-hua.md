---
description: 对一些DOM绘制进行节流,防重复,防抖,以性能优化
---

# DOM动画的一些优化

#### 硬节流

```
// Some code
// Some code
const throttleByAnimationFrame = (func) => {
  let callArgs = null  
  const frameFunc = () => {
    const currentCallArgs = callArgs
    callArgs = null
    func.apply(null, currentCallArgs)
  }
  return (...args) => {
    !callArgs && requestAnimationFrame(frameFunc) //防止一个渲染进程中多次注册
    callArgs = args
  }
}

var sayNum = throttleByAnimationFrame((a,b,c)=>{console.log(a,b,c)})

```

Func 为下次重绘时要执行的效果

throttleByAnimationFrame的返回为实际上一些拖动等动作时的函数执行

当触发时会判断是否上一个requestAnimationFrame调用还在执行,存在的话抛弃掉. 更新requestAnimationFrame实际传参,然后等待下一个浏览器重绘时间根据此参数执行.&#x20;

#### 选择性节流

```
// Some code
const throttleByAnimationFrame = (func) => {
  let callArgs = null  
  const frameFunc = () => {
    const currentCallArgs = callArgs
    func.apply(null, currentCallArgs)
  }
  return (...args) => {
    args == callArgs || requestAnimationFrame(frameFunc) //如果新来的参数和旧参数相同则抛弃
    callArgs = args
  }
}

var sayNum = throttleByAnimationFrame((a,b,c)=>{console.log(a,b,c)})
```

当触发时会判断是否已经有一个同样参数的requestAnimationFrame调用存在,存在的话抛弃掉. 不存在的话更新requestAnimationFrame实际传参,然后等待下一个浏览器重绘时间根据此参数执行.&#x20;

### window.requestAnimationFrame(callback);

> `callback`下一次重绘之前更新动画帧所调用的函数(即上面所说的回调函数)。该回调函数会被传入[`DOMHighResTimeStamp`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMHighResTimeStamp)参数，该参数与[`performance.now()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Performance/now)的返回值相同，它表示`requestAnimationFrame()` 开始去执行回调函数的时刻。
>
> #### 返回值 <a href="#fan-hui-zhi" id="fan-hui-zhi"></a>
>
> 一个 `long` 整数，请求 ID ，是回调列表中唯一的标识。是个非零值，没别的意义。你可以传这个值给 [`window.cancelAnimationFrame()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/cancelAnimationFrame) 以取消回调函数。

---
description: LocalStorage存取复杂类型对象
---

# LocalStorage存取对象

```
// Some code

const createLocalStorageStateStore = ({ //整体批量管理状态信息(存取对象型数据)
  keyPrefix = 'STATE', keyListKey = '@@KEY_LIST', // 没个对象对应的专属名称
  setItem = (key,value) => localStorage.setItem(`${keyPrefix}|${key}`, JSON.stringify(value)),
  getItem = (key) => JSON.parse(localStorage.getItem(`${keyPrefix}|${key}`)),
  removeItem = (key) => localStorage.removeItem(`${keyPrefix}|${key}`)
} = {}) =>{
  let persistState = {}
  let persistKeyList = [] //以存贮信息 key列表
  
  
  const save = (state) => { //set the whole state, so maybe debounce a bit
      // __DEV__ && console.log('[save] state:', state)
    const keyList = Object.keys(state)
    for(const key of keyList){
      __DEV__ && persistState[ key ] !== state[ key ] && console.log('[save] save key:', key)
      persistState[ key ] !== state[ key ] && setItem(key, state[ key ]) // 如果未存储,则存储
    }
    for(const key of persistKeyList){
      __DEV__ && !state[ key ] && console.log('[save] drop key:', key)
      !state[ key ] && removeItem(key) //  检测旧的状态信息表,删除多余项
    }
    if (!isCompactArrayShallowEqual(keyList, persistKeyList)) { // 检测新的对象与旧的是否key表相同
      __DEV__ && console.log('[save] save keyList:', keyList)
      setItem(keyListKey, keyList) //不同则更新整体key表
    }
    persistState = state
    persistKeyList = keyList
  }

  const load = () => { // get the whole state, so maybe load once on start
    try {
      const loadKeyList = getItem(keyListKey) || [] //获取key列表
      persistState = loadKeyList.reduce((o, key) => {
        o[ key ] = getItem(key)
        return o
      }, {})
      persistKeyList = loadKeyList // NOTE: set after load state success
      __DEV__ && console.log('[load] persistState:', persistState)
    } catch (error) { __DEV__ && console.log('[load] error:', error) }
    return persistState
  }

  return {
    save,
    load
  }
}

```

* 本质上就是讲对象拆分成一组键值对(命名加入对象标识---对象名|key名)进行批量存储维护
* 值得注意的是对象的整体更新要去除不存在于新对象内旧对象的key值
* 整个方法每次初始化都生成一个存贮对象逻辑组.(我在说什么鸟语
* isCompactArrayShallowEqual 为判断数组等价的方法
* 嵌套对象和函数方法不清真
* 所以为啥不以JASON形式存储呢,可能是ES6不清真吧


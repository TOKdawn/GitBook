# Diff算法

### VUE 中的 Diff 实现

Diff 算法比较只会进行同层进行,不会进行跨层比较

#### 相同节点判断

```
function sameVnode (a, b) {
  return (
    a.key === b.key &&  // key值
    a.tag === b.tag &&  // 标签名
    a.isComment === b.isComment &&  // 是否为注释节点
    // 是否都定义了data，data包含一些具体信息，例如onclick , style
    isDef(a.data) === isDef(b.data) &&  
    sameInputType(a, b) // 当标签是<input>的时候，type必须相同
  )
}
```

首先比较newVnode 和oldVnode 的`sel` 属性(VDOM的构建描述’dev’, ’ul.className’, ’li#idName’ 等),如果节点不同说明Vnode被完全改变了,直接替换为newVnode 因为Diff算法为同层比较,所以不需要进行children的比较

#### 节点合并

```
function patchVnode(oldVnode, newVnode){
  const el = newVnode.el = oldVnode.el //合并引用
  let i, oldCh = oldVnode.children, ch = newVnode.children
  if (oldVnode === newVnode) return //如果严格一致直接返回
  if (oldVnode.text !== null && newVnode.text !== null && oldVnode.text !== newVnode.text) { // 文本节点处理
    api.setTextContent(el, newVnode.text)
  }else{
    updateEle(el, newVnode, oldVnode) //更新节点
    if (oldCh && ch && oldCh !== ch) {
          updateChildren(el, oldCh, ch)
    }else if (ch){
          createEle(newVnode) 
    }else if (oldCh){
          api.removeChildren(el)
    }
  }
}
```

* 找到对应的真实dom，称为el
* 判断newVnode和oldVnode是否指向同一个对象，如果是，那么直接return
* 如果他们都有文本节点并且不相等，那么将el的文本节点设置为newVnode的文本节点。
* 如果oldVnode有子节点而newVnode没有，则删除el的子节点
* 如果oldVnode没有子节点而newVnode有，则将newVnode的子节点真实化之后添加到el
* 如果两者都有子节点，则执行updateChildren函数比较子节点

#### 子节点比较

有子节点 —>无子节点 无子节点 —>有子节点 子节点—>文本节点 文本节点—>子节点 这几种情况都比较简单,关键点在于有子节点—>有子节点这种情况的比较 为了方便理解可以虚拟成两个数组进行比较

```
function sameVnode (a, b) {//节点相同判断
  return (
    a.key === b.key &&  // key值
    a.tag === b.tag &&  // 标签名
    a.isComment === b.isComment &&  // 是否为注释节点
    // 是否都定义了data，data包含一些具体信息，例如onclick , style
    isDef(a.data) === isDef(b.data) &&  
    sameInputType(a, b) // 当标签是<input>的时候，type必须相同
  )
}

function patchVnode(oldVnode, newVnode){
  const el = newVnode.el = oldVnode.el //合并引用
  let i, oldCh = oldVnode.children, ch = newVnode.children
  if (oldVnode === newVnode) return //如果严格一致直接返回
  if (oldVnode.text !== null && newVnode.text !== null && oldVnode.text !== newVnode.text) { // 文本节点处理
    api.setTextContent(el, newVnode.text)
  }else{
    updateEle(el, newVnode, oldVnode) //更新节点
    if (oldCh && ch && oldCh !== ch) {
          updateChildren(el, oldCh, ch)
    }else if (ch){
          createEle(newVnode) 
    }else if (oldCh){
          api.removeChildren(el)
    }
  }
}

function updateChildren(el,newChidlren,OldChidlren){ //挂载父节点

  let oldStartIdx = 0;//老节点的数组开始索引
  let oldEndIdx = OldChidlren.length - 1;  //老节点的数组结束索引
  let newStartIdx = 0;//新节点的数组开始索引
  let newEndIdx = newChidlren.length - 1; //新节点的数组结束索引
  
  let oldStartVnode = OldChidlren[0] //老的开始节点
  let oldEndVnode = OldChidlren[oldEndIdx]//老的结束节点
  let newStartVnode = newChidlren[0]//新的开始节点
  let newEndVnode = newChidlren[newEndIdx] //新的结束节点

  while(oldStartIdx<=oldEndIdx && newStartIdx<=newEndIdx){//结束条件,两组数据都处理完
    if (oldStartVnode == null) {   // 处理空节点
        oldStartVnode = OldChidlren[++oldStartIdx] 
    }else if (oldEndVnode == null) {
        oldEndVnode = OldChidlren[--oldEndIdx]
    }else if (newStartVnode == null) {
        newStartVnode = newChidlren[++newStartIdx]
    }else if (newEndVnode == null) {
        newEndVnode = newChidlren[--newEndIdx] 
        //所有操作其实都是在老列表上进行,整个列表会被分割为已经操作过的节点和未操作的节点两部分
        //操作过的节点组成最后结果列表,未操作过的列表继续作为旧列表进行匹配操作
    }else if(sameVnode(newStartVnode,oldStartVnode )){//头头相等
      patchVnode(newStartVnode,oldStartVnode) //直接合并,处理下一个
      oldStartVnode = OldChidlren[++oldStartIdx]
      newStartVnode = newChidlren[++newStartIdx]
    }else if(sameVnode(newEndVnode,oldEndVnode )){ //尾尾相等
      patchVnode(newEndVnode,oldEndVnode) //直接合并,处理上一个
      oldStartVnode = OldChidlren[--oldEndIdx]
      newStartVnode = newChidlren[--newEndIdx]
    }else if(sameVnode(oldStartVnode, newEndVnode)){ //老头等于新尾
      
      patchVnode(oldStartVnode, newEndVnode)
      api.insertBefore(parentElm, oldStartVnode.el, api.nextSibling(oldEndVnode.el)) //则把老头移到最后 这里的头尾都是虚拟头尾,参照双指针区间,不一定是实际列表尾
      oldStartVnode = OldChidlren[++oldStartIdx] //处理下一个老头
      newEndVnode = newChidlren[--newEndIdx]//处理下一个新尾
    }else if(sameVnode(oldEndVnode, newStartVnode)){ //老尾等于新头
      patchVnode(oldEndVnode, newStartVnode)//则吧老尾移到最头

      api.insertBefore(parentElm, oldEndVnode.el,oldStartVnode.el) 
      oldEndVnode = OldChidlren[--oldEndIdx] //处理上一个老尾
      newStartVnode = newChidlren[++newStartIdx]//处理下一个新头
    }else {//进行key值判断
      if (oldKeyToIdx === undefined){
        oldKeyToIdx = createKeyToOldIdx(OldChidlren, oldStartIdx, oldEndIdx) // 创建旧列表哈希表
      }
      idxInOld = oldKeyToIdx[newStartVnode.key] //在旧表中查询是否存在对应的节点
      if(!idxInOld){//不存在符合项
        api.insertBefore(parentElm,createEle(newStartVnode).el,oldStartVnode.el) //把新列表的头节点插入旧表最前面
        newStartVnode = newChidlren[++newStartIdx]//继续处理下一个
      }else{
        elmToMove = oldChidlren[idxInOld]//获取对应的节点
        if (elmToMove.sel !== newStartVnode.sel) {//如果为不为同一个DOM 
          api.insertBefore(parentElm, createEle(newStartVnode).el, oldStartVnode.el)//创建一个插入到头 这里是处理一些key值一样,但实际上还是不一样的节点
        }else{
          patchVnode(elmToMove, newStartVnode)//合并节点
          oldChidlren[idxInOld] = null//旧表对应位置置空
          api.insertBefore(parentElm, elmToMove.el, oldStartVnode.el)//把对应节点插入到旧表最头
        }
        newStartVnode = newChidlren[++newStartIdx]//继续处理下一个

      }
      if (oldStartIdx > oldEndIdx) {//旧表全处理完
          before = newChidlren[newEndIdx + 1] == null ? null : newChidlren[newEndIdx + 1].el
          addVnodes(parentElm, before, newChidlren, newStartIdx, newEndIdx)//把新表剩余的剩余元素都插入到旧表后
      }else if (newStartIdx > newEndIdx) {//新表处理完
          removeVnodes(parentElm, oldChidlren, oldStartIdx, oldEndIdx)//把旧表剩余的元素都删除
      }
    }
  }  
}
```

* 所有操作其实都是在老列表上进行,整个列表会被分割为已经操作过的节点和未操作的节点两部分
* 操作过的节点组成最后结果列表,未操作过的列表继续作为旧列表进行匹配操作
* 之后语境里的头尾都是根据双指针标识出来的待处理段的头尾
* 判断相等后的两项代表已处理完,指针要移动相应的进行移动,头处理则开始指针后移,尾处理则结束指针前移
* 判断相等也要执行patchVnode操作合并
* 头头相等,合并后插入旧表头,指针都后移
* 尾尾相等,合并后插入旧表尾,指针都前移
* 老头等于新尾,则把老头移到最后
* 老尾等于新头,则吧老尾移到最头
* 头尾都没有相等则以新头进行哈希表比较
* 没有哈希表要以旧列表key值构建
* 查到不到则直接新头插入旧表最前,然后新表开始指针后移
* 查到了,也要进行一下全等判断,确定相等后,把对应的节点移到旧表最开始,并且相应位置节点赋值为undefined.然后新表开始指针后移
* 为啥要再次进行全等判断呢,因为key值命中一次后,哈希表不会更新,key可能再次命中,而节点已经被移走,还有些key值重复等特殊情况
* 结束条件
* oldS > oldE表示oldChidlren先遍历完，那么就将多余的vCh根据index添加到dom中去
* S > E表示newChidlren先遍历完，那么就在真实dom中将区间为\[oldS, oldE]的多余节点删掉

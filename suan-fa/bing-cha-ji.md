---
description: 今天教大家做一道 插插鸡插饼插鸡饼插饼插鸡
---

# 饼插鸡



### union-find问题的API

initUF(N){构建N个触点}&#x20;

union(p,q){链接p和q}&#x20;

find(p){查找p所在的分量标识符}&#x20;

connected(p,q){判定p,q是否存在于同一个连通分量重}

&#x20;count(){返回分量的数量}



树的存储形式

* 纯数组下标对应集合值方便查询,但是归并操作效率(慢)要把所有的节点都进行更改
* 树存储归并操作快(根节点插入另一个树)但是寻根查询就慢
* 所以可以加权插入(小树插入到大树上)
* 或者做路径压缩,保证树高为二

### 优化点

[朋友圈](https://leetcode-cn.com/problems/friend-circles/)

```
var findCircleNum = function(M) {
    var UF = Array.from({length: M.length}, (v, i) => i); 构建树
    function uncion(q,p){
        while(UF[q]!=q){
            q = UF[q];//查根
        }
         while(UF[p]!=p){
            p = UF[p];//查根
        }
        if(p ==q){
            return false
        }else{
            return true
        }
    }
    function connect(q,p){ // 其实应该按chilren的长度判定谁向谁归并
        var chilren = [];
        while(UF[q]!=q){
            chilren.push[q]//记录子节点
            q = UF[q];//向根回溯
        }
        chilren.map((v)=>{UF[v] = q})//压缩树
        chilren = []
        while(UF[p]!=p){
            chilren.push[p]//归并两棵树
            p = UF[p];
        }
        chilren.map((v)=>{UF[v] = q})
        UF[p] = q
        return;
    }
    for(var i = 0;i<M.length;i++){
        for(var j =0;j<M.length;j++){
            if(M[i][j]){//连通
                if(uncion(UF[i],UF[j])){//判定是否不连通
                    connect(UF[i],UF[j])//进行连通
                }
            }
        }
    }
    var num = 0;
    UF.map((v,i)=>{
        if(v==i){//查找最后树的数量
            num++
        }
    })

    return num
};
```

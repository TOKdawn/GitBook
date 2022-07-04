---
description: '[823. 带因子的二叉树](https://leetcode.cn/problems/binary-trees-with-factors/)'
---

# 记忆化搜索DP - 因子二叉树



本题其实是很标准的动归,和打家劫舍系列类似,以某节点做根的合理树数量为左右合理根数量相乘.&#x20;

与其他动归不同的时,dp方程取值下标是不定的,打家劫舍系列只要查询前两个状态就行了,&#x20;

而本体需要查某个叶子数值所对应的合理构成数量,而这个合理叶子数值对应在dp数组内的下标是不定的,所以要再引入一个map表做映射关系,即记忆化搜索. 关系为:&#x20;

确定了某可能的叶子取值 — —> 通过map表查询是否存在此叶子值.如果存在,就再查询其对应的dp数组下标 — —> 然后再通过下标去dp数组内取到此值的合理构成树数量.

&#x20;树的所有节点取值都是原数组的某个值,所以map表的初始化关系就是&#x20;

key: arr\[i] — —> value: i&#x20;

而dp数组初始值都为1,即这个值自己构建成一个单节点树&#x20;

因为是因子树,所以跟肯定比叶子都大,因此顺序遍历即可&#x20;

取谋值为根,遍历查询所有比它小的值作为一侧叶子,看是否存在可整除的另一半叶子&#x20;

如果存在,就通关映射表查询合理构成树,然后相乘加上初始值1.就是这个取值的合理构成树了

&#x20;最后特殊处理超大值,计算后对 base取余,&#x20;

结果为所有节点的合理构成树总量加一起,要注意的是每次相加都可能超过base所有每次相加后要再进行一次取余操作.



```
var numFactoredBinaryTrees = function (arr) {
  let n = arr.length;
  let dp = new Array(n).fill(1)
  let base = 1000000007
  let valueMap = new Map();
  arr.sort((a, b) => {
    return a - b
  })
  for (let i = 0; i < n; i++) { //建立 值 - 以当前值为根树 关系
    valueMap[arr[i]] = i //给定一个值,取到其在dp数组中的下标
  }
  for (let i = 1; i < n; i++) {
    // 取某值做根
    for( let j = 0; j<i; j++){
      //遍历所有小于该根的因子
      if(arr[i] % arr[j] == 0){//如果因子合理,可与另一个因子组成叶子节点
        if(valueMap.hasOwnProperty(arr[i] / arr[j])){
          dp[i] = (dp[i] + dp[j] * dp[valueMap[arr[i] /arr[j]]] ) % base //当前数值自己做根 + 所有合理的左右子树数量
        }
      }
    }
  }
  let res = 0;
  for (let i = 0; i < n; ++i)
  {
      res = (res + dp[i]) % base;
  }
  return res;
};
```

---
description: 可变与不可变区间和
---

# 区间和与线段树

#### 区间和问题

对于不变数组的区间和问题可以通过前缀数组解决,

即一开始构建一个前缀和数组,当实际要求区间和的时候直接用传入的下标相减就行.要注意边界处理,为了方便一般在前缀数组头置0,然后取下标时用right+1 - left ,因为区间和包含开始节点,而直接减会把开始节点减掉.

#### [303. 区域和检索 - 数组不可变](https://leetcode.cn/problems/range-sum-query-immutable/)

```
var NumArray = function(nums) {
    this.subArr = [0]
    this.sub = 0
    nums.forEach(item =>{
        this.sub+=item
        this.subArr.push(this.sub)
    }) 
    console.log(this.subArr)
};

NumArray.prototype.sumRange = function(left, right) {
    return this.subArr[right+1] - this.subArr[left]
};
```

#### 记忆化 [560. 和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)

和为k的子数组,其实参照上题的思路就是 subArr\[right] - subArr\[left] == k

当然为了统计总共有多少种,则需要引入一个map做为累加和结果的记录.

遍历前缀数组,作为结束下标,然后通过减 k 取可以满足的开始下标取值, 存在的话则记录结果 + 1 ,不存在就记录下当前节点的前缀和结果到map里,以为之后的查询做准备

```
var subarraySum = function(nums, k) {
  let subArr = []
  const mp = new Map();
  let res = 0
  let sub = 0
  mp.set(0,1)
  for(let I = 0; I < nums.length; I++){
    sub+=nums[I]
    subArr[i] = sub
  }
  subArr.forEach(item=>{
   if(mp.has(item - k)){
    res += mp.get(item - k);
   }
   if(mp.has(item)){
      mp.set(item, mp.get(item) + 1);
   } else {
    mp.set(item,1)
   }
  })
  return res
}; 
```

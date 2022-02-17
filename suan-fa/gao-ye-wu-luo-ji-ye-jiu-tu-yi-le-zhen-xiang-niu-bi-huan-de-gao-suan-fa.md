---
description: >-
  做前端最大的感受就是每天都在跟各种业务逻辑打交道,做做这搞搞那,但最终好像都是在学一个又一个工具的使用,无非这个工具土里土气,那个工具花里胡哨而已,莫得灵魂,真要感受编程的快乐啊,还是得去玩算法....
---

# 搞业务逻辑也就图一乐,真想牛逼还得搞算法

### 小知识备忘

* log(n)复杂度相当与二分法查变量,效率比n还高哦
* 有log(n)这里时间复杂度要求的问题多为二分或递归,因为只有这些才会产生log(n)
* JavaScript 语言里Number.MIN\_VALUE竟然是最小的正值,而不是最小的Number,有一说一,这语音真的不适合拿来写算法.
* let dp = Array.from(new Array(n+1),() => new Array(m+1).fill(false)); //生成n\*m二维数组,填充false

###

### 利用已知,反向思考.

对于很多问题我们应当聚焦于利用已知资源,最常见的就遍历的时候要从遍历过的元素中挖掘信息,而不是考虑那些会被未遍历项影响的结果.

1. 典型的一个题目便是求众数[多数元素](https://leetcode-cn.com/problems/majority-element/) ,当我们关注到一个元素的时候只能知道它在之前出现过多少次,没法确定它是一个众数,因为之后可能有更多数量的其他元素. 但是我们能确定它不是一个众数,只要之前遍历过的那些元素里有超过他数目的其他元素.\
   所以我们就能很好把求众数的问题化解为求不是众数的值,最后退出谁是众数

### 找到华点

**盲生你找到了华点** 有一说一,这种东西只能靠多练,以及老天爷赏脸.

1. [相交链表](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/) 两数组拼接长度相等,且有 **私A+公+私B** == **私B+公+私A**.真实太巧妙了
2. [环形链表](https://leetcode-cn.com/problems/linked-list-cycle/) 快慢指针,跑一遍就肯定能相遇,爽到.

### DP

动态规划的核心思想在于最大化利用计算量,保证所有计算结果都建立在之前的计算结果之上. **子状态,状态迁移方程,结束条件**.抓住着三点就能学好动态规划. 而很多动态规划的问题也都掺杂着一点贪心的思路在里边,这类问题一般对于一个状态都有几种解法,我们要就当前状态找到最好解法,然后这个状态的最优解就可以当做已知条件,为后面状态判定最优解的时候提供帮助了.如一下几题:

* [爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)
* [买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)
* [打家劫舍](https://leetcode-cn.com/problems/house-robber/)

### 深搜

深搜搜时候一般都是从下向上遍历节点,然后把当前节点(包含其下所有节点)符合条件的数向上传递.研究树问题的时候一般都是关注这几点:\\

* 判定是不是到末端了,作递归的结束处理
* 先通过函数调用,传递当前节点的左右叶做向下挖掘.
* 根据上一步函数调用的返回值,配合对应业务逻辑,判定当前节点符不符合结果.
* 把子节点返回的结果加上自己的结果传做函数的返回值递给上一级 [路径总和 III](https://leetcode-cn.com/problems/path-sum-iii/)

###

[两数之和](https://leetcode-cn.com/problems/two-sum/) ——— 反向索引&#x20;

很简单的问题,一个数组里找相加为特定值的两个元素下标. 常规解法都绕不开两次循环对应两个下标的寻找,而构建反向索引的思路则是在一个循环内同时处理了(第一个元素下标记录)&&(构建一个 数组元素值对应此元素下标 索引关系的数组)两件事,这样就可以避开第二次循环 转而用js自己的索引机制代替

```
给定一个整数数组 nums和一个目标值 target，请你在该数组中找出和为目标值的那两个整数，并返回他们的数组下标。
你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。
var twoSum = function(nums, target) {
	let Arrlength = nums.length;
	let elementToIndex = []
	for(var i = 0;i < Arrlength; i++){
		var diff = target - nums[i]
		if(elementToIndex[diff] != undefined){
			return [i,elementToIndex[diff]]
		} 
		elementToIndex[nums[i]] = i 
	}
};
```

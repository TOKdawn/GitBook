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



[罗马数字转整数](https://leetcode-cn.com/problems/roman-to-integer/)

&#x20;状态姬,虽然没必要,但是我一定要用状态姬,要不然怎么能体现我搞过状态姬..

```
        var res = 0;
        var NUM = 0;
        const start = char => {
            if (char == 'I') {
                NUM = 1
                return notEnd;
            } else if (char == 'X') {
                NUM = 10
                return notEnd;
            } else if (char == 'C') {
                NUM = 100
                return notEnd
            }
            if (char == 'V') {
                res += 5
                return start
            }
            if (char == 'L') {
                res += 50
                return start
            }
            if (char == 'D') {
                res += 500
                return start
            }
            if (char == 'M') {
                res += 1000
                return start
            }
        }
        const notEnd = char => {
            if (char == 'V' && NUM == 1) {
                res += 4
                return start
            } else if (char == 'X' && NUM == 1) {
                res += 9
                return start
            } else if (char == 'L' && NUM == 10) {
                res += 40
                return start
            } else if (char == 'C' && NUM == 10) {
                res += 90
                return start
            } else if (char == 'D' && NUM == 100) {
                res += 400
                return start
            } else if (char == 'M' && NUM == 100) {
                res += 900
                return start
            } else {
                res += NUM
                return start(char)
            }
        }
        var romanToInt = function (s) {
            res = 0;
            var state = start;
            for (var c of s.split('')) {
                state = state(c);
            }
            state('') //后判定状态姬,再传一个,保证最后一个字符的状态确定
            return res
        };
```

[只出现一次的数字](https://leetcode-cn.com/problems/single-number/)&#x20;

如果我们对 0 和二进制位做 XOR 运算，得到的仍然是这个二进制位 a ^ 0 = a\
如果我们对相同的二进制位做 XOR 运算，返回的结果是 0 a ^ a = 0\
XOR 满足交换律和结合律 a^b^a = (a^a)^b = 0^b = b 所以我们只需要将所有的数进行 XOR 操作，得到那个唯一的数字。 麻了,麻了...

```
var singleNumber = function(nums) {
    var res = 0;
    for(var i = 0;i<nums.length;i++){
        res ^=nums[i]
    }
    return res
};
```

---
description: 动归那点事
---

# 买股票

动态规范问题三要素:

* DP方程 &#x20;

三种状态,天数,买卖次数,当前是否持有股票 DP\[day]\[num]\[true] DP\[day]\[num]\[false]

* 迁移方程&#x20;

不操作:DP\[day]\[num]\[True] = Math.max(DP\[day-1]\[num]\[True]

卖出: DP\[day-1]\[num-1]\[False] - prices\[Daynow]) DP\[day]\[num]\[False] = Math.max(DP\[day-1]\[num]\[False]

买入 :DP\[day-1]\[num]\[True] + prices\[Daynow])

* 初始状态

对于Day = 0 ;收益一定为0 对于Day = 0 ; num = 1;不合法; 对于Day = 0; True不合法;

### [121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)&#x20;

只允许交易一次,所以num为定值1,带入方程 DP\[day]\[1]\[1] = Math.max(DP\[day-1]\[1]\[1],DP\[day-1]\[0]\[0] - prices\[Daynow]) DP\[day]\[1]\[False] = Math.max(DP\[day-1]\[0]\[False],DP\[day-1]\[1]\[1] + prices\[Daynow]) 对于DP\[day-1]\[0]\[0],意义为前day-1天,进行了0次交易,没持有股票的情况.很明显值肯定为 0 所以有程序:

```
var maxProfit = function(prices) {
    var dp = Array.from(new Array(prices.length),()=>[])
    dp[0][0] = [0];
    dp[0][1] = - prices[0]
    for (var i = 1; i < prices.length; i++) {
        dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + prices[i]);
        dp[i][1] = Math.max(dp[i-1][1],  -prices[i]);
    }
    return dp[prices.length-1][0]; 
};
```

dp\[i-1]\[0] 和 dp\[i-1]\[1] 和 已知条件prices\[i-1] —> dp\[i]\[0] dp\[i-1]\[1] 和 已知条件prices\[i-1] —> dp\[i]\[1] 所以就没必要通过数组存储两个变量存前置状态即可,加上特判代码如下:

```
var maxProfit = function(prices) {
    if(prices.length == 0){
        return 0
    }
    var dp_num_F = 0;
    var dp_num_T = - prices[0]
    for (var i = 0; i < prices.length; i++) {
        dp_num_F = Math.max(dp_num_F, dp_num_T + prices[i]);
        dp_num_T = Math.max(dp_num_T,  -prices[i]);
    }
   return dp_num_F ;  
};
```

### [122. 买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)&#x20;

允许多次交易,num为无限,带入方程 跟上一题的区别就在于DP\[day-1]\[0]\[0],变成了DP\[day-1]\[num]\[0],之前可以进行任意次交易,那在计算收益的时候把之前的收益加上即可

```
var maxProfit = function(prices) {
    if(prices.length == 0){
        return 0
    }
    var dp_num_F = 0;
    var dp_num_T = - prices[0]
    for (var i = 0; i < prices.length; i++) {
        dp_num_F = Math.max(dp_num_F, dp_num_T + prices[i]);
        dp_num_T = Math.max(dp_num_T,  dp_num_F -prices[i]);
    }
   return dp_num_F ;  
};
```

### [123. 买卖股票的最佳时机 III](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/)&#x20;

对于此题DP方程为三维的,拆分成DP\_T和DP\_F两个二维数组更方便管理 次题num为2;理论上应该再引入一个循环去处理num,但因为就循环两次,所以直接写两遍逻辑也可以

```javascript
    var maxProfit = function(prices) {
           if(prices.length <= 1){
                return 0
            }
        var DP_T = Array.from(new Array(prices.length+1),()=>[]);//天数和购买次数
        var DP_F = Array.from(new Array(prices.length+1),()=>[]);
        DP_F[0][0] = 0; //没持有股票
        DP_T[0][0] = -prices[0];//第一天就买了
        DP_F[0][1] = 0;//第一天就买了又买了
        DP_T[0][1] = -prices[0];//第一天买了又卖了又买了
        for(var i = 1;i<=prices.length;i++){
            //DP为现在有的收益,对于没持有股票的时候就是净收益,持有股票的时候为 收益-本金

            DP_F[i][0] = Math.max(DP_F[i-1][0],DP_T[i-1][0] + prices[i-1] )
            //不操作或者,把第一次买的股票今天卖掉

            DP_T[i][0] = Math.max(DP_T[i-1][0], - prices[i-1])
            //不操作或者,买入一只股票

            DP_F[i][1] = Math.max(DP_F[i-1][1],DP_T[i-1][1] + prices[i-1] )
            //不操作或者,或者把买入的股票卖掉

            DP_T[i][1] = Math.max(DP_T[i-1][1],DP_F[i-1][0] - prices[i-1] )
            //不操作或者,或者在原有收益的基础上买入股票
        }
        return DP_F[prices.length][1]
};
```

同理我们可以进行优化,抛弃DP数组存储:

```
    var maxProfit = function(prices) {
        if( 1 >= prices.length){return 0}
        var  DP_F_Zero = DP_F_One = 0; 
        var  DP_T_Zero = DP_T_One  = -prices[0];
        for(var i = 1;prices.length>=i;i++){
            DP_F_Zero = Math.max(DP_F_Zero, DP_T_Zero + prices[i-1] )
            DP_T_Zero  = Math.max(DP_T_Zero , -prices[i-1] )
            DP_F_One = Math.max( DP_F_One,DP_T_One + prices[i-1] )
            DP_T_One = Math.max(DP_T_One,DP_F_Zero - prices[i-1] )
        }
        return DP_F_One
};
```

### [309. 最佳买卖股票时机含冷冻期](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)&#x20;

跟第二题唯一的区别就是一个是\_\_DP\_F\[i-1]\_\_ ,一个是\_\_DP\_F\[i-2]\_\_

```
var maxProfit = function(prices) {
    if(prices.length <= 1){return 0 }
    var DP_T = [-prices[0]]
    var DP_F = [0]
    for (var i = 1; i < prices.length; i++) {
        if(1 == i){
            DP_F[i] = Math.max(DP_F[i-1], DP_T[i-1] + prices[i]);
            DP_T[i] = Math.max(DP_T[i-1],  -prices[i]);
        }else{
            DP_F[i] = Math.max(DP_F[i-1], DP_T[i-1] + prices[i]);
            DP_T[i] = Math.max(DP_T[i-1], DP_F[i-2] - prices[i]);
        }
    }
    return  DP_F[prices.length-1]; 
};
```

### [714. 买卖股票的最佳时机含手续费](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)&#x20;

第二题的变种.每次买入的时候减个手续费就行

```
var maxProfit = function(prices, fee) {
        if(prices.length == 0){
        return 0
    }
    var dp_num_F = 0;
    var dp_num_T = - prices[0] - fee
    for (var i = 0; i < prices.length; i++) {
        dp_num_F = Math.max(dp_num_F, dp_num_T + prices[i]);
        dp_num_T = Math.max(dp_num_T,  dp_num_F - prices[i]-fee);
    }
   return dp_num_F ; 
};
```

### [188. 买卖股票的最佳时机 IV](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/)

按照第三题的思路加入一个循环处理购买次数即可: 但是这样写的话会导致内存爆炸.JS好弱啊Orz

```
        var maxProfit = function(k, prices) {
       
       if(prices.length <= 1){
            return 0
        }

    var DP_T = Array.from(new Array(prices.length+1),()=>new Array(k+1).fill(-prices[0]));//天数和购买次数
    var DP_F = Array.from(new Array(prices.length+1),()=>new Array(k+1).fill(0));
    for(var i = 1;i<=prices.length;i++){
        for(var j = 1;j<=k;j++){ 
            DP_T[i][j] = Math.max(DP_T[i-1][j],DP_F[i-1][j-1] - prices[i-1] )
            DP_F[i][j] = Math.max(DP_F[i-1][j],DP_T[i-1][j] + prices[i-1] )
        }

    }
    return DP_F[prices.length][k]
};
```

所以就要在以前的不用DP数组的版本去改造,还有就是压缩K的值,因为一次交易由买入和卖出构成，至少需要两天。所以说有效的限制 k 应该不超过 n/2

```
        var maxProfit = function(k, prices) {
            if (k > prices.length / 2) {
                k = Math.floor(prices.length / 2); 
			
            }
        var obj = Array.from(new Array(k+1),() => ({
            'DP_F': 0,
            'DP_T': -prices[0]
        }))
        for(var i = 0;prices.length>i;i++){
            for(var j = 1;j<=k;j++){
                obj[j].DP_F = Math.max(obj[j].DP_F, obj[j].DP_T + prices[i] )
                obj[j].DP_T = Math.max(obj[j].DP_T, obj[j-1].DP_F- prices[i] )
            }
        }
        return  obj[k].DP_F
};
```

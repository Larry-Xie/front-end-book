# 完全背包

[https://leetcode-cn.com/problems/coin-change/solution/dai-ma-sui-xiang-lu-dai-ni-xue-tou-wan-q-80r7/](https://leetcode-cn.com/problems/coin-change/solution/dai-ma-sui-xiang-lu-dai-ni-xue-tou-wan-q-80r7/)

![](https://assets.leetcode-cn.com/aliyun-lc-upload/users/carlsun-2/avatar\_1586162243.png?x-oss-process=image%2Fresize%2Ch\_38%2Cw\_38%2Fformat%2Cwebp)

## 「代码随想录」带你学透完全背包！【322. 零钱兑换】

[代码随想录L6](https://leetcode-cn.com/u/carlsun-2/)发布于 2021-09-038.2k[C++](https://leetcode-cn.com/topic/cpp/)[Go](https://leetcode-cn.com/topic/golang/)[Java](https://leetcode-cn.com/topic/java/)[JavaScript](https://leetcode-cn.com/topic/javascript/)[Python](https://leetcode-cn.com/topic/python/)[Python3](https://leetcode-cn.com/topic/python3/)

![动态规划-背包问题](https://pic.leetcode-cn.com/1625278189-FkZgZp-file\_1625278189326)

**不少同学对动态规划还处于朦胧状态，我特意录了一期视频，讲一讲**[**动态规划解题方法论**](https://leetcode-cn.com/link/?target=https://www.bilibili.com/video/BV13Q4y197Wg)，这里详细介绍了动规五部曲，相信结合本篇题解，会对你学习动态规划有所帮助。

### 思路

在[动态规划：518.零钱兑换II](https://leetcode-cn.com/link/?target=https://programmercarl.com/0518.%E9%9B%B6%E9%92%B1%E5%85%91%E6%8D%A2II.html)中我们已经兑换一次零钱了，这次又要兑换，套路不一样！

题目中说每种硬币的数量是无限的，可以看出是典型的完全背包问题。

动规五部曲分析如下：

1. 确定dp数组以及下标的含义

**dp\[j]：凑足总额为j所需钱币的最少个数为dp\[j]**

1. 确定递推公式

得到dp\[j]（考虑coins\[i]），只有一个来源，dp\[j - coins\[i]]（没有考虑coins\[i]）。

凑足总额为j - coins\[i]的最少个数为dp\[j - coins\[i]]，那么只需要加上一个钱币coins\[i]即dp\[j - coins\[i]] + 1就是dp\[j]（考虑coins\[i]）

所以dp\[j] 要取所有 dp\[j - coins\[i]] + 1 中最小的。

递推公式：dp\[j] = min(dp\[j - coins\[i]] + 1, dp\[j]);

1. dp数组如何初始化

首先凑足总金额为0所需钱币的个数一定是0，那么dp\[0] = 0;

其他下标对应的数值呢？

考虑到递推公式的特性，dp\[j]必须初始化为一个最大的数，否则就会在min(dp\[j - coins\[i]] + 1, dp\[j])比较的过程中被初始值覆盖。

所以下标非0的元素都是应该是最大值。

代码如下：

```
vector<int> dp(amount + 1, INT_MAX);
dp[0] = 0;
```

1. 确定遍历顺序

本题求钱币最小个数，**那么钱币有顺序和没有顺序都可以，都不影响钱币的最小个数。**。

所以本题并不强调集合是组合还是排列。

**如果求组合数就是外层for循环遍历物品，内层for遍历背包**。

**如果求排列数就是外层for遍历背包，内层for循环遍历物品**。

在动态规划专题我们讲过了求组合数是[动态规划：518.零钱兑换II](https://leetcode-cn.com/link/?target=https://programmercarl.com/0518.%E9%9B%B6%E9%92%B1%E5%85%91%E6%8D%A2II.html)，求排列数是[动态规划：377. 组合总和 Ⅳ](https://leetcode-cn.com/link/?target=https://programmercarl.com/0377.%E7%BB%84%E5%90%88%E6%80%BB%E5%92%8C%E2%85%A3.html)。

**所以本题的两个for循环的关系是：外层for循环遍历物品，内层for遍历背包或者外层for遍历背包，内层for循环遍历物品都是可以的！**

那么我采用coins放在外循环，target在内循环的方式。

本题钱币数量可以无限使用，那么是完全背包。所以遍历的内循环是正序

综上所述，遍历顺序为：coins（物品）放在外循环，target（背包）在内循环。且内循环正序。

1. 举例推导dp数组

以输入：coins = \[1, 2, 5], amount = 5为例

![322.零钱兑换](https://pic.leetcode-cn.com/1630638463-IQRUNJ-file\_1630638463143)

dp\[amount]为最终结果。

### C++代码

以上分析完毕，C++ 代码如下：

```
// 版本一
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        vector<int> dp(amount + 1, INT_MAX);
        dp[0] = 0;
        for (int i = 0; i < coins.size(); i++) { // 遍历物品
            for (int j = coins[i]; j <= amount; j++) { // 遍历背包
                if (dp[j - coins[i]] != INT_MAX) { // 如果dp[j - coins[i]]是初始值则跳过
                    dp[j] = min(dp[j - coins[i]] + 1, dp[j]);
                }
            }
        }
        if (dp[amount] == INT_MAX) return -1;
        return dp[amount];
    }
};
```

对于遍历方式遍历背包放在外循环，遍历物品放在内循环也是可以的，我就直接给出代码了

```
// 版本二
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        vector<int> dp(amount + 1, INT_MAX);
        dp[0] = 0;
        for (int i = 1; i <= amount; i++) {  // 遍历背包
            for (int j = 0; j < coins.size(); j++) { // 遍历物品
                if (i - coins[j] >= 0 && dp[i - coins[j]] != INT_MAX ) {
                    dp[i] = min(dp[i - coins[j]] + 1, dp[i]);
                }
            }
        }
        if (dp[amount] == INT_MAX) return -1;
        return dp[amount];
    }
};
```

### 总结

细心的同学看网上的题解，**可能看一篇是遍历背包的for循环放外面，看一篇又是遍历背包的for循环放里面，看多了都看晕了**，到底两个for循环应该是什么先后关系。

能把遍历顺序讲明白的文章几乎找不到！

这也是大多数同学学习动态规划的苦恼所在，有的时候递推公式很简单，难在遍历顺序上！

但最终又可以稀里糊涂的把题目过了，也不知道为什么这样可以过，反正就是过了，哈哈

那么这篇文章就把遍历顺序分析的清清楚楚。

[动态规划：518.零钱兑换II](https://leetcode-cn.com/link/?target=https://programmercarl.com/0518.%E9%9B%B6%E9%92%B1%E5%85%91%E6%8D%A2II.html)中求的是组合数，[动态规划：377. 组合总和 Ⅳ](https://leetcode-cn.com/link/?target=https://programmercarl.com/0377.%E7%BB%84%E5%90%88%E6%80%BB%E5%92%8C%E2%85%A3.html)中求的是排列数。

**而本题是要求最少硬币数量，硬币是组合数还是排列数都无所谓！所以两个for循环先后顺序怎样都可以！**

这也是我为什么要先讲518.零钱兑换II 然后再讲本题即：322.零钱兑换，这是Carl的良苦用心那。

相信大家看完之后，对背包问题中的遍历顺序又了更深的理解了。

### 其他语言版本

Java：

```
class Solution {
    public int coinChange(int[] coins, int amount) {
        int max = Integer.MAX_VALUE;
        int[] dp = new int[amount + 1];
        //初始化dp数组为最大值
        for (int j = 0; j < dp.length; j++) {
            dp[j] = max;
        }
        //当金额为0时需要的硬币数目为0
        dp[0] = 0;
        for (int i = 0; i < coins.length; i++) {
            //正序遍历：完全背包每个硬币可以选择多次
            for (int j = coins[i]; j <= amount; j++) {
                //只有dp[j-coins[i]]不是初始最大值时，该位才有选择的必要
                if (dp[j - coins[i]] != max) {
                    //选择硬币数目最小的情况
                    dp[j] = Math.min(dp[j], dp[j - coins[i]] + 1);
                }
            }
        }
        return dp[amount] == max ? -1 : dp[amount];
    }
}
```

Python：

```
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        '''版本一'''
        # 初始化
        dp = [amount + 1]*(amount + 1)
        dp[0] = 0
        # 遍历物品
        for coin in coins:
            # 遍历背包
            for j in range(coin, amount + 1):
                dp[j] = min(dp[j], dp[j - coin] + 1)
        return dp[amount] if dp[amount] < amount + 1 else -1
    
    def coinChange1(self, coins: List[int], amount: int) -> int:
        '''版本二'''
        # 初始化
        dp = [amount + 1]*(amount + 1)
        dp[0] = 0
        # 遍历物品
        for j in range(1, amount + 1):
            # 遍历背包
            for coin in coins:
                if j >= coin:
                	dp[j] = min(dp[j], dp[j - coin] + 1)
        return dp[amount] if dp[amount] < amount + 1 else -1
```

Go：

```
// 版本一, 先遍历物品,再遍历背包
func coinChange1(coins []int, amount int) int {
	dp := make([]int, amount+1)
	// 初始化dp[0]
	dp[0] = 0
	// 初始化为math.MaxInt32
	for j := 1; j <= amount; j++ {
		dp[j] = math.MaxInt32
	}

	// 遍历物品
	for i := 0; i < len(coins); i++ {
		// 遍历背包
		for j := coins[i]; j <= amount; j++ {
			if dp[j-coins[i]] != math.MaxInt32 {
				// 推导公式
				dp[j] = min(dp[j], dp[j-coins[i]]+1)
				//fmt.Println(dp,j,i)
			}
		}
	}
	// 没找到能装满背包的, 就返回-1
	if dp[amount] == math.MaxInt32 {
		return -1
	}
	return dp[amount]
}

// 版本二,先遍历背包,再遍历物品
func coinChange2(coins []int, amount int) int {
	dp := make([]int, amount+1)
	// 初始化dp[0]
	dp[0] = 0
	// 遍历背包,从1开始
	for j := 1; j <= amount; j++ {
		// 初始化为math.MaxInt32
		dp[j] = math.MaxInt32
		// 遍历物品
		for i := 0; i < len(coins); i++ {
			if j >= coins[i] && dp[j-coins[i]] != math.MaxInt32 {
				// 推导公式
				dp[j] = min(dp[j], dp[j-coins[i]]+1)
				//fmt.Println(dp)
			}
		}
	}
	// 没找到能装满背包的, 就返回-1
	if dp[amount] == math.MaxInt32 {
		return -1
	}
	return dp[amount]
}

func min(a, b int) int {
	if a < b {
		return a
	}
	return b
}
```

Javascript：

```
const coinChange = (coins, amount) => {
    if(!amount) {
        return 0;
    }

    let dp = Array(amount + 1).fill(Infinity);
    dp[0] = 0;

    for(let i =0; i < coins.length; i++) {
        for(let j = coins[i]; j <= amount; j++) {
            dp[j] = Math.min(dp[j - coins[i]] + 1, dp[j]);
        }
    }

    return dp[amount] === Infinity ? -1 : dp[amount];
}
```

## 背包问题力扣完整攻略

只要按如下顺序刷题，相信会帮你在学习背包问题的路上少走很多弯路！

* 0-1背包
  * [01背包问题理论基础二维dp数组](https://leetcode-cn.com/link/?target=https://programmercarl.com/%E8%83%8C%E5%8C%85%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%8001%E8%83%8C%E5%8C%85-1.html)
  * [01背包问题理论基础一维dp数组（滚动数组）](https://leetcode-cn.com/link/?target=https://programmercarl.com/%E8%83%8C%E5%8C%85%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%8001%E8%83%8C%E5%8C%85-2.html)
  * [416.分割等和子集](https://leetcode-cn.com/link/?target=https://programmercarl.com/0416.%E5%88%86%E5%89%B2%E7%AD%89%E5%92%8C%E5%AD%90%E9%9B%86.html)
  * [1049.最后一块石头的重量 II](https://leetcode-cn.com/link/?target=https://programmercarl.com/1049.%E6%9C%80%E5%90%8E%E4%B8%80%E5%9D%97%E7%9F%B3%E5%A4%B4%E7%9A%84%E9%87%8D%E9%87%8FII.html)
  * [494.目标和](https://leetcode-cn.com/link/?target=https://programmercarl.com/0494.%E7%9B%AE%E6%A0%87%E5%92%8C.html)
  * [474.一和零](https://leetcode-cn.com/link/?target=https://programmercarl.com/0474.%E4%B8%80%E5%92%8C%E9%9B%B6.html)
* 完全背包
  * [动态规划：关于完全背包，你该了解这些！](https://leetcode-cn.com/link/?target=https://programmercarl.com/%E8%83%8C%E5%8C%85%E9%97%AE%E9%A2%98%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%80%E5%AE%8C%E5%85%A8%E8%83%8C%E5%8C%85.html)
  * [518. 零钱兑换 II](https://leetcode-cn.com/link/?target=https://programmercarl.com/0518.%E9%9B%B6%E9%92%B1%E5%85%91%E6%8D%A2II.html)
  * [377.组合总和Ⅳ.](https://leetcode-cn.com/link/?target=https://programmercarl.com/0377.%E7%BB%84%E5%90%88%E6%80%BB%E5%92%8C%E2%85%A3.html)
  * [70.爬楼梯进阶版](https://leetcode-cn.com/link/?target=https://programmercarl.com/0070.%E7%88%AC%E6%A5%BC%E6%A2%AF%E5%AE%8C%E5%85%A8%E8%83%8C%E5%8C%85%E7%89%88%E6%9C%AC.html)
  * [322. 零钱兑换](https://leetcode-cn.com/link/?target=https://programmercarl.com/0322.%E9%9B%B6%E9%92%B1%E5%85%91%E6%8D%A2.html)
  * [279.完全平方数](https://leetcode-cn.com/link/?target=https://programmercarl.com/0279.%E5%AE%8C%E5%85%A8%E5%B9%B3%E6%96%B9%E6%95%B0.html)
  * [139.单词拆分](https://leetcode-cn.com/link/?target=https://programmercarl.com/0139.%E5%8D%95%E8%AF%8D%E6%8B%86%E5%88%86.html)
* 多重背包
  * [动态规划：关于多重背包，你该了解这些！](https://leetcode-cn.com/link/?target=https://programmercarl.com/%E8%83%8C%E5%8C%85%E9%97%AE%E9%A2%98%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%80%E5%A4%9A%E9%87%8D%E8%83%8C%E5%8C%85.html)
* [背包问题总结篇](https://leetcode-cn.com/link/?target=https://programmercarl.com/%E8%83%8C%E5%8C%85%E6%80%BB%E7%BB%93%E7%AF%87.html)

***

**大家好，我是程序员Carl，点击**[**我的头像**](https://leetcode-cn.com/link/?target=https://programmercarl.com/)，查看力扣详细刷题攻略，你会发现相见恨晚！

**如果感觉题解对你有帮助，不要吝啬给一个👍吧！**

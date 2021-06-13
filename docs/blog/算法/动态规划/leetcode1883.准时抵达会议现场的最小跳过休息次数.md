

## 题源

Leetcode链接：https://leetcode-cn.com/problems/minimum-skips-to-arrive-at-meeting-on-time

难度系数：Hard




## 题意

给你一个整数 hoursBefore ，表示你要前往会议所剩下的可用小时数。要想成功抵达会议现场，你必须途经 n 条道路。道路的长度用一个长度为 n 的整数数组 dist 表示，其中 dist[i] 表示第 i 条道路的长度（单位：千米）。另给你一个整数 speed ，表示你在道路上前进的速度（单位：千米每小时）。

当你通过第 i 条路之后，就必须休息并等待，直到 下一个整数小时 才能开始继续通过下一条道路。注意：你不需要在通过最后一条道路后休息，因为那时你已经抵达会议现场。

例如，如果你通过一条道路用去 1.4 小时，那你必须停下来等待，到 2 小时才可以继续通过下一条道路。如果通过一条道路恰好用去 2 小时，就无需等待，可以直接继续。
然而，为了能准时到达，你可以选择 跳过 一些路的休息时间，这意味着你不必等待下一个整数小时。注意，这意味着与不跳过任何休息时间相比，你可能在不同时刻到达接下来的道路。

例如，假设通过第 1 条道路用去 1.4 小时，且通过第 2 条道路用去 0.6 小时。跳过第 1 条道路的休息时间意味着你将会在恰好 2 小时完成通过第 2 条道路，且你能够立即开始通过第 3 条道路。
返回准时抵达会议现场所需要的 最小跳过次数 ，如果 无法准时参会 ，返回 -1 。



## 思路

**思路与算法**

我们用double类型的  `d[i][j]` 表示经过了 dist[0] 到 dist[i−1] 的 i段道路，并且跳过了 j 次的最短用时。

在进行状态转移时，我们考虑最后一段道路 dist[i−1]是否选择跳过。



**注意特殊样例：**

- 浮点数运算的细节

  这一部分非常重要，希望读者仔细阅读。

  根据 [IEEE 754 标准](https://baike.baidu.com/item/IEEE%20754)，浮点数在计算机中存储的精度是有限的，而本题中我们不可避免的会使用「浮点数运算」以及「向上取整」运算，如果强行忽略产生的计算误差，会得到错误的结果。

  举一个简单的例子，假设使用的语言中「向上取整」运算的函数为 ceil，下面的运算：

  ceil(8.0 + 1.0 / 3 + 1.0 / 3 + 1.0 / 3)

  应当是 9，而计算机会给出 10。这是因为浮点数误差导致。



### 代码

```java

    /**
     * 取模
     */
    private static final int MOD = (int)1e9 + 7;
    /**
     * 精度
     */
    private static final double EPS = 1e-8;

    /**
     * 考虑精度问题的ceil函数
     * 特殊 1.00000001 返回 1
     *
     * @param d
     * @return
     */
    public int ceil(double d) {
        return (int)Math.ceil(d - EPS);
    }

    public static int compare(double a, double b) {
        double diff = a - b;
        return Math.abs(diff) < EPS ? 0 : (diff < 0 ? -1 : 1);
    }

    public int minSkips(int[] dist, int speed, int hoursBefore) {
        int n = dist.length;
        double[] h = new double[n + 1];
        for (int i = 0; i < n; i++) {
            h[i + 1] = dist[i] * 1.0 / speed;
        }
        double[][] d = new double[n + 1][n + 1];
        d[0][0] = 0;
        for (int i = 1; i <= n; i++) {
            d[i][0] = ceil(h[i] + d[i - 1][0]);
            for (int j = 1; j < i; j++) {
                // 最后一个跳不跳？不跳，跳
                // ERROR：ceil 在外层
                d[i][j] = Math.min(d[i - 1][j - 1] + h[i], ceil(d[i - 1][j] + h[i]));
            }
            d[i][i] = h[i] + d[i - 1][i - 1];
        }
        // PrintUtils.printMa2(d);
        for (int i = 0; i <= n; i++) {
            if (compare(d[n][i], hoursBefore) <= 0) {
                return i;
            }
        }
        return -1;
    }
```





## 思路2

我们可以将数组 dist 中的道路长度和 hoursBefore 都乘以 speed。由于方法一的代码中所有除法运算的除数都是 speed，因此这样做可以保证所有的除法运算的结果都是整数，从根本上避免浮点数误差。

见链接：

https://leetcode-cn.com/problems/minimum-skips-to-arrive-at-meeting-on-time/solution/minimum-skips-to-arrive-at-meeting-on-ti-dp7v/

中的方法二。

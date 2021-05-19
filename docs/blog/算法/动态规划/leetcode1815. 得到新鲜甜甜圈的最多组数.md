

#tag_复习



## 题源

Leetcode链接：https://leetcode-cn.com/problems/maximum-number-of-groups-getting-fresh-donuts/



难度系数：Hard


## 题意

有一个甜甜圈商店，每批次都烤 batchSize 个甜甜圈。这个店铺有个规则，就是在烤一批新的甜甜圈时，之前 所有 甜甜圈都必须已经全部销售完毕。给你一个整数 batchSize 和一个整数数组 groups ，数组中的每个整数都代表一批前来购买甜甜圈的顾客，其中 groups[i] 表示这一批顾客的人数。每一位顾客都恰好只要一个甜甜圈。

当有一批顾客来到商店时，他们所有人都必须在下一批顾客来之前购买完甜甜圈。如果一批顾客中第一位顾客得到的甜甜圈不是上一组剩下的，那么这一组人都会很开心。

你可以随意安排每批顾客到来的顺序。请你返回在此前提下，最多 有多少组人会感到开心。



- `1 <= batchSize <= 9`
- `1 <= groups.length <= 30`
- `1 <= groups[i] <= 109`





## 思路1





**状态压缩+HashMap存离散状态+记忆化搜索**

本题问题为：如何组合，有最多组的和 模 batchSize 余 0 ？

**难点：状态使用dp [2^30]，太大存不下。方法是使用位压缩存。**



可以用一个 long long 进行状态压缩，每一项数量不会超过 30 ，那么每 5 位表示一个余数的数量，最多占用 40位（1∼8)。



**但是使用dp [2^(5*8)]，依然太大存不下。方法是使用HashMap存离散状态。**



**注意特殊样例：**



### 代码

```java
    public int maxHappyGroups(int bs, int[] g) {
        long state = 0;
        int ans0 = 0;
        int n = g.length;
        // 使用位表示元素
        for (int i = 0; i < n; i++) {
            int t = (g[i] % bs);
            state += 1L << (t * 5);
        }
        // 余为0的
        ans0 += (state & ((1L << 5) - 1));

        Map<Long, Integer> map = new HashMap<>();
        // 初始化
        map.put(0L, 0);

        return ans0 + dfs(state, 0, map, bs);
    }

    public int dfs(long state, int pre, Map<Long, Integer> map, int bs) {
        int res = map.getOrDefault(state, -1);
        if (res != -1) {
            return res;
        }
        res = 0;
        for (int i = 1; i < bs; i++) {
            long t = (state >> (i * 5)) & ((1L << 5) - 1);
            if (t > 0) {
                res = Math.max(res, (pre == 0 ? 1 : 0) + dfs(state - (1L << (i * 5)), (pre + i) % bs, map, bs));
            }
        }
        map.put(state, res);
        return res;
    }

```





## 思路2

**状态压缩+整数映射压缩+记忆化搜索**



**方法二是使用 <u>整数映射</u> 存离散状态。**


注意到 batchSize ≤9，一个容易想到的思路是对所有的 nums[i]，统计 nums[i]mod  batchSize  所出现的频次，得出频次数组 freq。

研究一下我们所采用的时间系统，一天有 24 小时，一小时有 60 分钟，一分钟有 60 秒，一秒有 1000 毫秒。我们可以用类似 1:12:59:31.100 的格式来定义一个时间间隔，表示 1 天 12 小时 59 分 31 秒 100 毫秒。我们可以把它 对应到一个数字 133171100（豪秒），表示经过了 133171100 毫秒。这两种表示方式是 ***\*一一对应\**** 的。这是一种特殊的进位计数方式，最低的位（毫秒）采用 1000 进一，然后是 60 进一（秒 →→ 分钟），……。

 我们可以采用类似的思路来表示 freq 数组。首先我们统计所有的 nums[i]，得出初始的 freq 数组 freq0。注意到，我们每选取一个顾客加入队尾，freq[i]只会减小，因此 freq[i]≤freq0[i] 。因此，我们可以在最低的位（位 0) 采用每 freq0[0]+1 进一的方式，然后位 1 采用每 freq0[1]+1  进一的方式，如此等等。通过这样的方式，我们可以把一个特定 freq 数组映射成一个 **唯一的整数**。



这个**唯一的整数**，最大是多少？

即把30分成8个数，使得(x0+1)×(x1+1)×...×(x7+1)最大。也就是把38分成8个数的最大积（可用动态规划思路求出，见divideMaxProduct函数）。最优解是分的越均匀越大，5^6 \* 4^2=250,000 。





### 记忆化搜索/递归代码

```java
    public int maxHappyGroups(int bs, int[] g) {
        // 数量数组
        int[] freq0 = new int[bs];
        // 权重数组
        int[] w = new int[bs];
        int n = g.length;
        // 余数的个数，最大30
        for (int i = 0; i < n; i++) {
            freq0[(g[i] % bs)]++;
        }

        // 求权重数组
        w[0] = 1;
        for (int i = 1; i < bs; i++) {
            // 变成进制
            freq0[i]++;
            w[i] = w[i - 1] * freq0[i];
        }

        // 最大的状态
        int[] dp = new int[250000];
        // 初始化
        Arrays.fill(dp, -1);
        dp[0] = 0;

        return freq0[0] + dfs(dp, w[bs - 1] - 1, 0, w, freq0, bs);
    }

    // public int[] getOrigin(int c, int[] base, int bs) {
    //     // 求原数组
    //     int[] origin = new int[bs];
    //     for (int i = 1; i < bs; i++) {
    //         origin[i] = c % base[i];
    //         c /= base[i];
    //     }
    //     return origin;
    // }

    public int dfs(int[] dp, int c, int preSum, int[] w, int[] freq0, int bs) {
        if (dp[c] != -1) {
            return dp[c];
        }
        // int[] origin = getOrigin(c, base, bs);

        dp[c] = 0;
        for (int i = 1; i < bs; i++) {
            // 每位（余数）对应的数目
            int bit = c / w[i - 1] % freq0[i];
            if (bit > 0) {
                dp[c] = Math.max(dp[c], (preSum == 0 ? 1 : 0) + dfs(dp, c - w[i - 1], (preSum + i) % bs, w, freq0, bs));
            }
        }
        return dp[c];
    }
```



### 非递归代码

来自官方思路

```java

    public int maxHappyGroups(int bs, int[] g) {
        // 数量数组
        int[] freq0 = new int[9];
        // 整数对应的数量数组
        int[] freq = new int[9];
        // 权重数组
        int[] w = new int[9];
        int n = g.length;
        // 余数的个数，最大30
        for (int i = 0; i < n; i++) {
            freq0[(g[i] % bs)]++;
        }

        // 求权重数组（bs==1时越界！！！所以数组大小都定义为9了）
        w[1] = 1;
        for (int i = 2; i < bs; i++) {
            w[i] = w[i - 1] * (freq0[i - 1] + 1);
        }

        // 最大范围
        int range = w[bs - 1] * (freq0[bs - 1] + 1);
        // bs==1时，range = 0，需特殊处理
        if (bs == 1) {
            range = 1;
        }

        // 最大的状态
        int[] dp = new int[250000];
        // 初始化
        dp[0] = 0;

        for (int fmask = 1; fmask < range; fmask++) {
            int preSum = 0;
            for (int i = 1; i < bs; i++) {
                freq[i] = fmask / w[i] % (freq0[i] + 1);
                preSum = (preSum + (freq0[i] - freq[i]) * i) % bs;
            }
            for (int i = 1; i < bs; i++) {
                if (freq[i] > 0) {
                    dp[fmask] = Math.max(dp[fmask], (preSum == 0 ? 1 : 0) + dp[fmask - w[i]]);
                }
            }

        }
        return freq0[0] + dp[range - 1];
    }

```




## 题源

Leetcode链接：https://leetcode-cn.com/problems/number-of-different-subsequences-gcds

难度系数：Hard




## 题意

给你一个由正整数组成的数组 nums 。

数字序列的 最大公约数 定义为序列中所有整数的共有约数中的最大整数。

例如，序列 [4,6,16] 的最大公约数是 2 。
数组的一个 子序列 本质是一个序列，可以通过删除数组中的某些元素（或者不删除）得到。

例如，[2,5,10] 是 [1,2,1,2,4,1,5,10] 的一个子序列。
计算并返回 nums 的所有 非空 子序列中 不同 最大公约数的 数目 。

- `1 <= nums.length <= 105`
- `1 <= nums[i] <= 2 * 105`



## 思路

类素数筛法（见《算法竞赛入门经典》P178）

枚举所有可能的最大公约数，判断是否为某个子序列的公约数。
因此，我们要解决的问题就是如何判断一个数是否为某个子序列的公约数。
我们可以采用类似素数筛的方式，枚举每一个数i，判断2i、3i、4*i…..的公约数是否为i，如果为i，则贡献加一。
筛法的复杂度为n/1 + n/2 + n/3 + … + n/n，渐进为O(n * logn)，而gcd的复杂度为O(logn)，所以总复杂度为O(n * logn * logn)。





**注意特殊样例：**



### 代码

```java
    /**
     * 求最大公约数：
     * <p>
     * 原理：欧几里得的辗转相除计算法则
     * <p>
     * 补充：guava里面有更快地方法，可直接调用
     *
     * @param a
     * @param b
     * @return
     */
    public int gcd(int a, int b) {
        int r = 1;
        while (b != 0) {
            r = a % b;
            a = b;
            b = r;
        }
        return a;
        // return b == 0 ? a : gcd(b, a % b);
    }

    public int countDifferentSubsequenceGCDs(int[] a) {
        int[] num = new int[200010];
        int n = a.length, ans = 0;
        int maxn = 0;
        for (int i = 0; i < n; i++) {
            maxn = Math.max(maxn, a[i]);
            num[a[i]]++;
            if (num[a[i]] == 1) {
                ans++;
            }
        }
        for (int i = 1; i <= maxn; i++) {
            if (num[i] == 0) {
                int g = 0;
                for (int j = i * 2; j <= maxn; j += i) {
                    if (num[j] != 0) {
                        g = gcd(g, j);
                        if (g == i) {
                            ans++;
                            break;
                        }
                    }
                }
            }
        }
        return ans;
    }

```






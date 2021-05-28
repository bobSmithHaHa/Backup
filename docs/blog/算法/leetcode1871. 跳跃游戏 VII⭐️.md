

## 题源

Leetcode链接：

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/jump-game-vii



难度系数：中等


## 题意

给你一个下标从 0 开始的二进制字符串 s 和两个整数 minJump 和 maxJump 。一开始，你在下标 0 处，且该位置的值一定为 '0' 。当同时满足如下条件时，你可以从下标 i 移动到下标 j 处：

i + minJump <= j <= min(i + maxJump, s.length - 1) 且
s[j] == '0'.
如果你可以到达 s 的下标 s.length - 1 处，请你返回 true ，否则返回 false 。







## 思路1

使用记录之前可到达的点（升序），求当前点是否可达，并记录。

即lowerBound函数。

**注意特殊样例：越界情况**



### 代码

```java
    /**
     * 在 a[l, r] 中找第一个满足BiPredicate<Integer, Integer> p的元素。
     * 数组元素的排列情况：前部分不满足，后部分满足；
     * <p>
     * 特殊情况：
     * - 数组元素都满足：返回l
     * - 数组元素都不满足：返回r+1（可能越界）
     *
     * @param a
     * @param l
     * @param r
     * @param v
     * @param p (ai,v) 参数1：数组元素，参数2：对比的值
     * @return
     */
    public static int findFirstTrue(int a[], int l, int r, int v, BiPredicate<Integer, Integer> p) {
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (!p.test(a[mid], v)) {
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return l;
    }

    public int lowerBound(int nums[], int l, int r, int v) {
        return findFirstTrue(nums, l, r, v, (ai, value) -> ai >= value);
    }

    public boolean canReach(String s, int minJump, int maxJump) {
        int n = s.length();
        int[] a = new int[n];
        a[0] = 0;
        int cur = 1;
        for (int i = 1; i < n; i++) {
            if (s.charAt(i) == '0') {
                int l = i - maxJump;
                int r = i - minJump;
                int idl = lowerBound(a, 0, cur - 1, l);
                if (idl >= cur || a[idl] > r) {
                    continue;
                }
                a[cur++] = i;
            }
        }
        return a[cur - 1] == n - 1;
    }
```





## 思路2

使用队列记录可达的点，并根据可达点扩充新点。记录进队列点的最大值，每个点只进一次队列，O(n)复杂度。



### 代码

```c++
class Solution {
public:
    queue<int> q;
    bool canReach(string s, int minJump, int maxJump) {
        int n = s.size();
        int a = minJump, b = maxJump;
        if (s[n - 1] == '1') return false;
        while (!q.empty()) q.pop();
        q.push(0);
        int R = 0;
        while (!q.empty()) {
            int k = q.front(); q.pop();
            if (k == n - 1) return true;
            for (int i = max(R + 1, k + a); i <= min(k + b, n - 1); i ++)
                if (s[i] == '0') {
                    q.push(i);
                }
            R = max(R, k + b);
        }
        return false;
    }
};
```



## 思路3

来自官方：**动态规划 + 前缀和优化**

判定s[i]是否可达，只要判定[i-maxJump,i-minJump]内部的点是否可以即可（注意边界）。
d[i]表示i位置是否可达，只要上述区间有一个为1，那么s[i]即为可达，利用d数组的前缀和可以快速计算上述区间和。



### 代码

```c++

    public boolean canReach(String s, int minJump, int maxJump) {
        int n = s.length();
        int[] sum = new int[n];
        sum[0] = 1;

        int[] d = new int[n];
        d[0] = 1;

        for (int i = 1; i < n; i++) {
            if (s.charAt(i) == '0') {
                int l = i - maxJump, r = i - minJump;
                l = Math.max(l, 0);
                if (r >= 0) {
                    int pre = (l == 0) ? sum[r] : sum[r] - sum[l - 1];
                    d[i] = pre > 0 ? 1 : 0;
                }
            }
            sum[i] = sum[i - 1] + d[i];
            // System.out.println("d[i] = " + d[i] + ",sum[i] = " + sum[i]);
        }
        return d[n - 1] == 1;
    }
```






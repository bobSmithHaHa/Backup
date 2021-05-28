

## 题源

Leetcode链接：https://leetcode-cn.com/problems/maximum-subarray-min-product/



难度系数：中等


## 题意

一个数组的 最小乘积 定义为这个数组中 最小值 乘以 数组的 和 。

比方说，数组 [3,2,5] （最小值是 2）的最小乘积为 2 * (3+2+5) = 2 * 10 = 20 。
给你一个正整数数组 nums ，请你返回 nums 任意 非空子数组 的最小乘积 的 最大值 。由于答案可能很大，请你返回答案对  109 + 7 取余 的结果。

请注意，最小乘积的最大值考虑的是取余操作 之前 的结果。题目保证最小乘积的最大值在 不取余 的情况下可以用 64 位有符号整数 保存。

子数组 定义为一个数组的 连续 部分。







## 思路

选择枚举右边界：使用单调栈



**注意特殊样例：**

最后别忘记出栈、



### 代码

```java

    public int maxSumMinProduct(int[] a) {
        int n = a.length;
        // 栈元素是递增的
        long[][] st = new long[n][2];
        //
        int p = -1;
        long ans = 0, sum = 0;
        for (int i = 0; i < n; i++) {
            if (p == -1 || a[i] > st[p][0]) {
                p++;
                st[p][0] = a[i];
                st[p][1] = a[i] + sum;
                sum = 0;
            } else if (a[i] == st[p][0]) {
                st[p][1] += a[i] + sum;
                sum = 0;
            } else {
                //出栈统计
                while (p >= 0 && a[i] < st[p][0]) {
                    // 比ai大的都要统计累积
                    sum += st[p][1];
                    ans = Math.max(ans, sum * st[p][0]);
                    p--;
                }
                i--;
            }
        }
        // 出栈统计
        while (p >= 0) {
            sum += st[p][1];
            ans = Math.max(ans, sum * st[p][0]);
            p--;
        }
        return (int)(ans % 1000000007);
    }
```






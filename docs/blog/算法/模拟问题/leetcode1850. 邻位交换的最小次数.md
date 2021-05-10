

## 题源

Leetcode链接：

https://leetcode-cn.com/problems/minimum-adjacent-swaps-to-reach-the-kth-smallest-number/



难度系数：中等


## 题意

给你一个表示大整数的字符串 num ，和一个整数 k 。

如果某个整数是 num 中各位数字的一个 排列 且它的 值大于 num ，则称这个整数为 妙数 。可能存在很多妙数，但是只需要关注 值最小 的那些。

例如，num = "5489355142" ：
第 1 个最小妙数是 "5489355214"
第 2 个最小妙数是 "5489355241"
第 3 个最小妙数是 "5489355412"
第 4 个最小妙数是 "5489355421"
返回要得到第 k 个 最小妙数 需要对 num 执行的 相邻位数字交换的最小次数 。

测试用例是按存在第 k 个最小妙数而生成的。





## 思路1

**模拟 + 贪心**

本题是一道由两个经典模型进行拼接得到的题目：

第一步我们需要求出比 num 大的第 k 个排列 numknumk。 

第二步我们需要将 num 通过交换操作得到 numk，每一步交换操作只能交换相邻的两个字符。

注意特殊样例：



### 代码

```java

    public void swap(char[] num, int i, int j) {
        char t = num[i];
        num[i] = num[j];
        num[j] = t;
    }
// 贪心的方法得到最少的交换次数。
    public int getMinSwaps(String num, int k) {
        int ans = 0;
        char[] src = num.toCharArray();
        char[] dest = getMinSwaps(num.toCharArray(), k);
        int n = src.length;
        // System.out.println("dest = " + String.copyValueOf(dest));
        for (int i = 0; i < n; i++) {
            if (src[i] != dest[i]) {
                for (int j = i + 1; j < n; j++) {
                    if (src[i] == dest[j]) {
                        while (j > i) {
                            swap(dest, j - 1, j);
                            ans++;
                            j--;
                        }
                        break;
                    }
                }
            }
        }
        return ans;
    }

    public char[] getMinSwaps(char[] s, int k) {
        while (k-- > 0) {
            next(s);
        }
        return s;
    }

// 求下一个排列，非专业写法，专业写法见nextPermutation函数
    public void next(char[] s) {
        int n = s.length;
        int max = s[n - 1];
        for (int i = n - 2; i >= 0; i--) {
            int cur = s[i];
            if (cur < max) {
                // 变成递增排序，并且找到第一个比cur大的值往前移
                for (int j = i; j < n - 1; j++) {
                    if (cur >= s[j + 1]) {
                        swap(s, j, j + 1);
                    } else {
                        for (; j >= i; j--) {
                            swap(s, j, j + 1);
                        }
                        break;
                    }
                }
                // ans++;
                // System.out.println("s= " + String.copyValueOf(s));
                return;
            } else {
                max = Math.max(max, s[i]);
                // 变成递增排序
                for (int j = i; j < n - 1; j++) {
                    if (s[j] > s[j + 1]) {
                        swap(s, j, j + 1);
                    } else {
                        break;
                    }
                }
            }
        }
    }
```






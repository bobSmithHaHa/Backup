

## 题源

Leetcode链接：https://leetcode-cn.com/problems/the-earliest-and-latest-rounds-where-players-compete/

难度系数：Hard




## 题意

n 名运动员参与一场锦标赛，所有运动员站成一排，并根据 最开始的 站位从 1 到 n 编号（运动员 1 是这一排中的第一个运动员，运动员 2 是第二个运动员，依此类推）。

锦标赛由多个回合组成（从回合 1 开始）。每一回合中，这一排从前往后数的第 i 名运动员需要与从后往前数的第 i 名运动员比拼，获胜者将会进入下一回合。如果当前回合中运动员数目为奇数，那么中间那位运动员将轮空晋级下一回合。

例如，当前回合中，运动员 1, 2, 4, 6, 7 站成一排
运动员 1 需要和运动员 7 比拼
运动员 2 需要和运动员 6 比拼
运动员 4 轮空晋级下一回合
每回合结束后，获胜者将会基于最开始分配给他们的原始顺序（升序）重新排成一排。

编号为 firstPlayer 和 secondPlayer 的运动员是本场锦标赛中的最佳运动员。在他们开始比拼之前，完全可以战胜任何其他运动员。而任意两个其他运动员进行比拼时，其中任意一个都有获胜的可能，因此你可以 裁定 谁是这一回合的获胜者。

给你三个整数 n、firstPlayer 和 secondPlayer 。返回一个由两个值组成的整数数组，分别表示两位最佳运动员在本场锦标赛中比拼的 最早 回合数和 最晚 回合数。

**提示：**

- `2 <= n <= 28`
- `1 <= firstPlayer < secondPlayer <= n`



## 思路

压缩dp。

状态2^28，使用HashMap保存离散状态。

状态转移需要使用递归保存转移后的状态。



**注意特殊样例：**

考虑等于first和second的情况。



### 代码

```java

    /**
     * 获取x的第i个二进制位
     *
     * @param x
     * @param i
     * @return
     */
    public static int getBit(int x, int i) {
        return (x >> i) & 1;
    }

    /**
     * 设置 x 的第 i 个二进制位为 v（取值0或1）
     *
     * @param x
     * @param i
     * @param v
     * @return
     */
    public static int setBit(int x, int i, int v) {
        if (v != 0 && v != 1) {
            throw new RuntimeException("只能设为0或1");
        }
        if (getBit(x, i) == 0 && v == 1) {
            x += (1 << i);
        }
        if (getBit(x, i) == 1 && v == 0) {
            x -= (1 << i);
        }
        return x;
    }

    public int[] earliestAndLatest(int n, int first, int second) {
        int ans = (1 << n) - 1;
        first--;
        second--;
      	//  mp1最早 回合数和 mp2最晚 回合数
        Map<Integer, Integer> mp1 = new HashMap<>(), mp2 = new HashMap<>();
        dfs(ans, mp1, mp2, n, first, second, n);
        return new int[] {mp1.get(ans), mp2.get(ans)};
    }

    public void dfs(int x, Map<Integer, Integer> mp1, Map<Integer, Integer> mp2, int n, int first, int second,
        int cur) {
        // println("cur = " + cur);
        if (mp1.containsKey(x)) {
            return;
        }
        if (cur == 2) {
            mp1.put(x, 1);
            mp2.put(x, 1);
            return;
        }
        int p = (cur) / 2;
        for (int i = 0, a = 0, b = n - 1; i < p; i++, a++, b--) {
            while (getBit(x, a) == 0) {
                a++;
            }
            while (getBit(x, b) == 0) {
                b--;
            }
            // System.out.println("a = " + a + ",b = " + b);
            if (a == first) {
                if (b == second) {
                    mp1.put(x, 1);
                    mp2.put(x, 1);
                    return;
                }
                break;
            }
        }
        List<Integer> list = new ArrayList<>((int)(Math.pow(2, p) + 0.5));
        dfsNum(x, n, first, second, p, 0, n - 1, list);
        int min = n, max = 0;
        for (Integer next : list) {
            dfs(next, mp1, mp2, n, first, second, (cur + 1) / 2);
            min = Math.min(min, mp1.get(next) + 1);
            max = Math.max(max, mp2.get(next) + 1);
        }
        mp1.put(x, min);
        mp2.put(x, max);
    }

    public void dfsNum(int x, int n, int first, int second, int p, int l, int r, List<Integer> list) {
        if (p == 0) {
            list.add(x);
            return;
        }
        while (getBit(x, l) == 0) {
            l++;
        }
        while (getBit(x, r) == 0) {
            r--;
        }
        if (l != first) {
            dfsNum(setBit(x, l, 0), n, first, second, p - 1, l + 1, r - 1, list);
        }
        if (r != second) {
            dfsNum(setBit(x, r, 0), n, first, second, p - 1, l + 1, r - 1, list);
        }
    }
```






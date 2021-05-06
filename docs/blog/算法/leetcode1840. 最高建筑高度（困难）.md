

## 题源

Leetcode链接：https://leetcode-cn.com/problems/maximum-building-height/

难度系数：Hard

（看了题解才完成）


## 题意

在一座城市里，你需要建 n 栋新的建筑。这些新的建筑会从 1 到 n 编号排成一列。

这座城市对这些新建筑有一些规定：

每栋建筑的高度必须是一个非负整数。
第一栋建筑的高度 必须 是 0 。
任意两栋相邻建筑的高度差 不能超过  1 。
除此以外，某些建筑还有额外的最高高度限制。这些限制会以二维整数数组 restrictions 的形式给出，其中 restrictions[i] = [idi, maxHeighti] ，表示建筑 idi 的高度 不能超过 maxHeighti 。

题目保证每栋建筑在 restrictions 中 至多出现一次 ，同时建筑 1 不会 出现在 restrictions 中。

请你返回 最高 建筑能达到的 最高高度 。



## 思路

见官方：https://leetcode-cn.com/problems/maximum-building-height/solution/zui-gao-jian-zhu-gao-du-by-leetcode-solu-axbb/



官方使用的两次遍历确实很好！自己开始使用的是删除一些没无效的限制方法，注意特殊样例：**最后的限制被前面的限制覆盖掉了**





### 代码

```java

    public int maxBuilding(int l, int[][] restrictions) {
        int ans = 0;
        int n = restrictions.length + 2;
        int[][] ma = new int[n][2];
        for (int i = 0; i < restrictions.length; i++) {
            System.arraycopy(restrictions[i], 0, ma[i], 0, restrictions[i].length);
        }
        // 增加限制 (1, 0)
        ma[n - 2][0] = 1;
        ma[n - 2][1] = 0;
        // 增加限制 (1, 0)
        ma[n - 1][0] = l;
        ma[n - 1][1] = l - 1;
        Arrays.sort(ma, Comparator.comparingInt(a -> a[0]));
        // PrintUtils.printMa2(ma);
        int j = 0;
        for (int i = 1; i < n; i++) {
            //限制过高无效
            if (ma[i][1] > ma[j][1] + (ma[i][0] - ma[j][0])) {
                continue;
            } else {
                // 限制合适
                if (ma[i][1] + (ma[i][0] - ma[j][0]) > ma[j][1]) {
                    j++;
                    System.arraycopy(ma[i], 0, ma[j], 0, ma[i].length);
                } else {
                    // 限制比之前的更低，之前的限制无效删除
                    while (ma[i][1] + (ma[i][0] - ma[j][0]) <= ma[j][1] && j > 0) {
                        j--;
                    }
                    j++;
                    System.arraycopy(ma[i], 0, ma[j], 0, ma[i].length);
                }
            }
        }
        // PrintUtils.printMa2(ma);
        n = j + 1;
        for (int i = 0; i < n - 1; i++) {
            int y = (ma[i + 1][0] - ma[i][0] + ma[i][1] + ma[i + 1][1]) / 2;
            ans = Math.max(ans, y);
        }
        // 最后的限制被前面的限制覆盖掉了（ERROR了2次）
        if (ma[n - 1][0] != l) {
            ans = Math.max(ans, ma[n - 1][1] + (l - ma[n - 1][0]));
        }
        return ans;
    }
```






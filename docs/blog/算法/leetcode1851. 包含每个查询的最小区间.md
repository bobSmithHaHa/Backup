

## 题源

Leetcode链接：https://leetcode-cn.com/problems/minimum-interval-to-include-each-query/



难度系数：Hard


## 题意

给你一个二维整数数组 intervals ，其中 intervals[i] = [lefti, righti] 表示第 i 个区间开始于 lefti 、结束于 righti（包含两侧取值，闭区间）。区间的 长度 定义为区间中包含的整数数目，更正式地表达是 righti - lefti + 1 。

再给你一个整数数组 queries 。第 j 个查询的答案是满足 lefti <= queries[j] <= righti 的 长度最小区间 i 的长度 。如果不存在这样的区间，那么答案是 -1 。

以数组形式返回对应查询的所有答案。













## 思路

平衡树操作：增删元素、求最小值。



将给定的区间和询问重新排序。

我们将每一个区间拆分成两个「事件」，询问对应一个「事件」，放入数组中进行升序排序。随后我们遍历这些「事件」：

如果我们遇到一个表示区间左端点的「事件」，那么将该区间的长度加入「有序集合」中；
如果我们遇到一个表示区间右端点的「事件」，那么将该区间的长度从「有序集合」中移除；
如果我们遇到一个表示询问的「事件」，那么答案即为「有序集合」中的最小值。



查询使用间接排序获取id。

**注意特殊样例：**没有区间的情况。



### 代码

```java

    /**
     * 间接排序
     * 按照数组元素大小排序，元素小的对应的下标在前面。
     * 比如a=[7,3,1,9]
     * 返回下标数组为：[2,1,0,3]，表示a[2]元素最小，其次a[1]、a[0]、a[3]。
     *
     * @param a
     * @return 下标数组
     */
    public static Integer[] indirectSort(int[] a) {
        Integer[] id = IntStream.range(0, a.length)
                                .boxed()
                                .toArray(Integer[]::new);
        Arrays.sort(id, Comparator.comparing(x -> a[x]));
        return id;
    }

    public boolean isLeft(int[][] ma, int i) {
        return ma[i][0] == ma[i][1];
    }

    public int[] minInterval(int[][] intervals, int[] queries) {
        Comparator<Pair<Integer, Integer>> c = Comparator.comparingInt(a -> (a.getValue() - a.getKey()));
        c = c.thenComparing(a -> a.getKey())
             .thenComparing(a -> a.getValue());
        TreeSet<Pair<Integer, Integer>> set = new TreeSet<>(c);
        int n = intervals.length;
        int[][] ma = new int[n * 2][3];
        for (int i = 0; i < n; i++) {
            ma[i * 2][0] = intervals[i][0];
            ma[i * 2][1] = intervals[i][0];
            ma[i * 2][2] = intervals[i][1];

            ma[i * 2 + 1][0] = intervals[i][1] + 1;
            ma[i * 2 + 1][1] = intervals[i][0];
            ma[i * 2 + 1][2] = intervals[i][1];
        }
        n = n * 2;
        Arrays.sort(ma, (a, b) -> a[0] - b[0]);
        // PrintUtils.printMa2(ma);
        int q = queries.length;
        Integer[] id = indirectSort(queries);
        int j = 0;
        int ans[] = new int[q];
        for (int i = 0; i < q; i++) {
            int value = queries[id[i]];
            while (j < n && ma[j][0] <= value) {
                Pair<Integer, Integer> pair = new Pair<>(ma[j][1], ma[j][2]);
                if (isLeft(ma, j)) {
                    set.add(pair);
                } else {
                    set.remove(pair);
                }
                j++;
            }
            ans[id[i]] = -1;
            if (!set.isEmpty()) {
                Pair<Integer, Integer> first = set.first();
                ans[id[i]] = first.getValue() - first.getKey() + 1;
            }
        }
        return ans;
    }
```







## 思路2

优先队列（最小堆）



**预处理排序**
对于查询，需要离线处理。
首先将查询列表从小向大排序，同时需要记录查询列表原本的位置信息。
将区间列表按照左端点从小到大排序，右端点从小到大排序。

**遍历查询，计算结果**
对于每一个查询：
首先将左端点符合条件的区间（即左端点小于或等于当前查询的值）插入到优先队列，优先队列按照区间长度，从小到大排序。
然后检查优先队列的堆顶，如果堆顶的区间的右端点满足条件（即右端点大于或者等于当前查询的值），即查询到结果。
如果不满足就弹出堆顶，继续查询下一个堆顶。
直到堆顶满足条件或者优先队列为空为止。



**注意特殊样例：**没有区间的情况。



### 代码

```java

```




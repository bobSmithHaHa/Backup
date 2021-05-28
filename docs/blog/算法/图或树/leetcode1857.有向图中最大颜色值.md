

## 题源

Leetcode链接：https://leetcode-cn.com/problems/largest-color-value-in-a-directed-graph/

难度系数：Hard




## 题意

给你一个 有向图 ，它含有 n 个节点和 m 条边。节点编号从 0 到 n - 1 。

给你一个字符串 colors ，其中 colors[i] 是小写英文字母，表示图中第 i 个节点的 颜色 （下标从 0 开始）。同时给你一个二维数组 edges ，其中 edges[j] = [aj, bj] 表示从节点 aj 到节点 bj 有一条 有向边 。

图中一条有效 路径 是一个点序列 x1 -> x2 -> x3 -> ... -> xk ，对于所有 1 <= i < k ，从 xi 到 xi+1 在图中有一条有向边。路径的 颜色值 是路径中 出现次数最多 颜色的节点数目。

请你返回给定图中有效路径里面的 最大颜色值 。如果图中含有环，请返回 -1 。





## 思路1

有向图环判断dfs + 遍历统计（dp）





### 代码

```java

    public int largestPathValue(String colors, int[][] edges) {
        int n = colors.length();
        ArrayList<LinkedList<Integer>> g = buildDirectedGraph(n, edges);
        int[][] num = new int[n][26];
        int[] vis = new int[n];

        int ans = 0;
        for (int i = 0; i < n; i++) {
            // 有环
            if (find(g, i, num, vis, colors)) {
                return -1;
            }
        }
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < 26; j++) {
                ans = Math.max(ans, num[i][j]);
            }
        }
        return ans;
    }

    /**
     * 有向图环判断 - 遍历每个连通块
     */
    public static final boolean find(ArrayList<LinkedList<Integer>> g, int v, int[][] num, int[] vis, String color) {
        // 之前无环的节点
        if (vis[v] == -1) {
            return false;
        }
        // 正在访问，存在环
        if (vis[v] == 1) {
            return true;
        }
        // 当前正在遍历
        vis[v] = 1;

        List<Integer> l = g.get(v);
        if (l != null) {
            for (Integer y : l) {
                if (find(g, y, num, vis, color) == true) {
                    return true;
                }
                for (int i = 0; i < 26; i++) {
                    num[v][i] = Math.max(num[v][i], num[y][i]);
                }
            }
        }
        num[v][color.charAt(v) - 'a']++;
        // 以 v 为起点遍历结点没有环。以后不需要再访问。
        vis[v] = -1;
        return false;
    }

    /**
     * 生成一个可变长度的二维数组：ArrayList的每个元素时链表
     * 可用于“图或树结构”的每个点的相连的点。
     *
     * @param n
     * @param <T>
     * @return
     */
    public <T> ArrayList<LinkedList<T>> newArrayListLinkedList(int n) {
        ArrayList<LinkedList<T>> l = new ArrayList<>(n);
        for (int i = 0; i < n; i++) {
            l.add(new LinkedList<T>());
        }
        return l;
    }

    /**
     * 建有向图
     */
    public ArrayList<LinkedList<Integer>> buildDirectedGraph(int n, int[][] edges) {
        ArrayList<LinkedList<Integer>> l = newArrayListLinkedList(n);
        for (int i = 0; i < n; i++) {
            l.add(new LinkedList<>());
        }
        for (int i = 0; i < edges.length; i++) {
            l.get(edges[i][0])
             .add(edges[i][1]);
        }
        return l;
    }
```







## 思路2

拓扑排序bfs + 遍历统计（dp）





### 代码

```java

    public int largestPathValue(String colors, int[][] edges) {
        int n = colors.length();
        int[] ans = new int[n];

        return topSort(n, edges, ans, colors);
    }

    public int topSort(int n, int[][] edges, int[] ans, String colors) {
        // 建图
        ArrayList<LinkedList<Integer>> g = buildDirectedGraph(n, edges);
        return topSort(g, n, ans, colors);
    }

    public static int topSort(ArrayList<LinkedList<Integer>> g, int n, int[] ans, String colors) {
        // 求入度数组
        int[] in = new int[n];
        for (LinkedList<Integer> l : g) {
            for (Integer v : l) {
                in[v]++;
            }
        }

        int[][] num = new int[n][26];
        // 入度为0的进队列
        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            if (in[i] == 0) {
                q.add(i);
            }
        }

        // 结果数组和下标
        int k = 0;

        //排序
        while (!q.isEmpty()) {
            int u = q.poll();
            ans[k++] = u;
            ++num[u][colors.charAt(u) - 'a'];
            LinkedList<Integer> l = g.get(u);
            if (null != l) {
                for (Integer v : l) {
                    --in[v];
                    for (int i = 0; i < 26; i++) {
                        num[v][i] = Math.max(num[v][i], num[u][i]);
                    }
                    if (in[v] == 0) {
                        q.add(v);
                    }
                }
            }
        }

        int res = 0;
        for (int i = 0; i < n; i++) {
            res = Math.max(res, Arrays.stream(num[i])
                                      .max()
                                      .getAsInt());
        }
        return k == n ? (res) : -1;
    }

    /**
     * 生成一个可变长度的二维数组：ArrayList的每个元素时链表
     * 可用于“图或树结构”的每个点的相连的点。
     *
     * @param n
     * @param <T>
     * @return
     */
    public <T> ArrayList<LinkedList<T>> newArrayListLinkedList(int n) {
        ArrayList<LinkedList<T>> l = new ArrayList<>(n);
        for (int i = 0; i < n; i++) {
            l.add(new LinkedList<T>());
        }
        return l;
    }

    /**
     * 建有向图
     */
    public ArrayList<LinkedList<Integer>> buildDirectedGraph(int n, int[][] edges) {
        ArrayList<LinkedList<Integer>> l = newArrayListLinkedList(n);
        for (int i = 0; i < edges.length; i++) {
            l.get(edges[i][0])
             .add(edges[i][1]);
        }
        return l;
    }

```






## 题源

Leetcode链接：

https://leetcode-cn.com/contest/weekly-contest-237/problems/single-threaded-cpu/

难度系数：中等




## 题意

给你一个二维数组 tasks ，用于表示 n 项从 0 到 n - 1 编号的任务。其中 tasks[i] = [enqueueTimei, processingTimei] 意味着第 i 项任务将会于 enqueueTimei 时进入任务队列，需要 processingTimei 的时长完成执行。

现有一个单线程 CPU ，同一时间只能执行 最多一项 任务，该 CPU 将会按照下述方式运行：

如果 CPU 空闲，且任务队列中没有需要执行的任务，则 CPU 保持空闲状态。
如果 CPU 空闲，但任务队列中有需要执行的任务，则 CPU 将会选择 执行时间最短 的任务开始执行。如果多个任务具有同样的最短执行时间，则选择下标最小的任务开始执行。
一旦某项任务开始执行，CPU 在 执行完整个任务 前都不会停止。
CPU 可以在完成一项任务后，立即开始执行一项新任务。
返回 CPU 处理任务的顺序。

示例 1：

输入：tasks = [[1,2],[2,4],[3,2],[4,1]]
输出：[0,2,3,1]
解释：事件按下述流程运行： 
- time = 1 ，任务 0 进入任务队列，可执行任务项 = {0}
- 同样在 time = 1 ，空闲状态的 CPU 开始执行任务 0 ，可执行任务项 = {}
- time = 2 ，任务 1 进入任务队列，可执行任务项 = {1}
- time = 3 ，任务 2 进入任务队列，可执行任务项 = {1, 2}
- 同样在 time = 3 ，CPU 完成任务 0 并开始执行队列中用时最短的任务 2 ，可执行任务项 = {1}
- time = 4 ，任务 3 进入任务队列，可执行任务项 = {1, 3}
- time = 5 ，CPU 完成任务 2 并开始执行队列中用时最短的任务 3 ，可执行任务项 = {1}
- time = 6 ，CPU 完成任务 3 并开始执行任务 1 ，可执行任务项 = {}
- time = 10 ，CPU 完成任务 1 并进入空闲状态

**提示：**

- `tasks.length == n`
- `1 <= n <= 105`
- `1 <= enqueueTimei, processingTimei <= 109`



## 思路1

模拟问题：使用优先队列模拟。（写的时候会有几个注意点，可以写一下）



### 代码

```java

    public int[] getOrder(int[][] tasks) {
        int n = tasks.length;

        int[] ans = new int[n];
        java.util.Map.Entry<Integer, java.util.Map.Entry<Integer, Integer>>[] entryEntry = new java.util.Map.Entry[n];
        // 排序的是（开始时间：（运行时间，下标））
        for (int i = 0; i < n; i++) {
            entryEntry[i] = new java.util.AbstractMap.SimpleEntry<>(tasks[i][0],
                new java.util.AbstractMap.SimpleEntry<>(tasks[i][1], i));
        }
        Arrays.sort(entryEntry, java.util.Map.Entry.comparingByKey());
        // PrintUtils.printMa(entryEntry);

        Comparator<Node> comparator =
            (a, b) -> (a.processingTime == b.processingTime) ? a.id - b.id : a.processingTime - b.processingTime;
        Queue<Node> q = new PriorityQueue<>(comparator);
        int i = 0, cur = 0, j = 0;
        while (i < n || (!q.isEmpty())) {
            for (; i < n; i++) {
                int enqueueTime = entryEntry[i].getKey();
                if (entryEntry[i].getKey() <= cur) {
                    inQueue(q, entryEntry, i);
                } else {
                    break;
                }
            }
            if (q.isEmpty()) {
                inQueue(q, entryEntry, i);
                cur = entryEntry[i].getKey();
                i++;
                continue;
            }

            Node node = q.poll();
            ans[j++] = node.id;
            //ERROR:使用cur + node.processingTime而不是node.enqueueTime + node.processingTime
            cur = cur + node.processingTime;
        }
        return ans;
    }

    public void inQueue(Queue<Node> q, java.util.Map.Entry<Integer, java.util.Map.Entry<Integer, Integer>>[] entryEntry,
        int i) {
        //进队列的
        q.add(new Node(entryEntry[i].getValue()
                                    .getKey(), entryEntry[i].getValue()
                                                            .getValue(), entryEntry[i].getKey()));
    }

    class Node {
        int processingTime;
        int id;
        int enqueueTime;

        Node(int p, int i, int e) {
            processingTime = p;
            id = i;
            enqueueTime = e;
        }
    }
```







## 思路2（官方）

区别于思路1，使用间接排序，减小代码难度，不需要新建结构体。



### 代码

```java

    public int[] getOrder(int[][] tasks) {
        int n = tasks.length;

        int[] ans = new int[n];
        // 间接排序,p[i]表示排序为i的下标是p[i]，tasks[p[i]]
        Integer[] p = new Integer[n];
        for (int i = 0; i < n; i++) {
            p[i] = i;
        }
        Arrays.sort(p, (a, b) -> (tasks[a][0] - tasks[b][0]));
        // PrintUtils.printMa(p);

        Comparator<Integer> comparator = (a, b) -> ((tasks[a][1] == tasks[b][1]) ? a - b : tasks[a][1] - tasks[b][1]);
        Queue<Integer> q = new PriorityQueue<>(comparator);
        int i = 0, cur = 0, j = 0;
        while (i < n || (!q.isEmpty())) {
            for (; i < n; i++) {
                if (tasks[p[i]][0] <= cur) {
                    q.add(p[i]);
                } else {
                    break;
                }
            }
            if (q.isEmpty()) {
                q.add(p[i]);
                cur = tasks[p[i]][0];
                i++;
                continue;
            }

            int id = q.poll();
            ans[j++] = id;
            //ERROR:使用cur + node.processingTime而不是node.enqueueTime + node.processingTime
            cur = cur + tasks[id][1];
        }
        return ans;
    }
```




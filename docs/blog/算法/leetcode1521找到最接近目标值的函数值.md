

## 题源

Leetcode：https://leetcode-cn.com/problems/find-a-value-of-a-mysterious-function-closest-to-target/

难度系数：Hard


## 题意

Winston 构造了一个的函数 func(arr, l, r) ：求arr数组下标l到r所有值的并值（&）。

他有一个整数数组 arr 和一个整数 target ，他想找到让 |func(arr, l, r) - target| 最小的 l 和 r 。

请你返回 |func(arr, l, r) - target| 的最小值。

请注意， func 的输入参数 l 和 r 需要满足 0 <= l, r < arr.length 。



示例 1：

输入：arr = [9,12,3,7,15], target = 5
输出：2
解释：所有可能的 [l,r] 数对包括 [[0,0],[1,1],[2,2],[3,3],[4,4],[0,1],[1,2],[2,3],[3,4],[0,2],[1,3],[2,4],[0,3],[1,4],[0,4]]， Winston 得到的相应结果为 [9,12,3,7,15,8,0,3,7,0,0,3,0,0,0] 。最接近 5 的值是 7 和 3，所以最小差值为 2 。
示例 2：

输入：arr = [1000000,1000000,1000000], target = 1
输出：999999
解释：Winston 输入函数的所有可能 [l,r] 数对得到的函数值都为 1000000 ，所以最小差值为 999999 。
示例 3：

输入：arr = [1,2,4,8,16], target = 0
输出：0





## 思路1

**枚举左节点**

枚举每个L，找到对应的R，使|func(arr, l, r) - target|最小。

对于每个L，如果R越大则func(arr, l, r)越小，可以使用二分查找求R。

快速求arr[l,r]的并值可使用线段树。

使用**二分+线段树**。



注意特殊样例：



### 代码

```java

    public int closestToTarget(int[] arr, int target) {
        //建树
        int[] tree = new int[arr.length << 2];
        build(tree, 1, arr, 0, arr.length - 1);
        // PrintUtils.printMa(tree);

        //求解
        int ans = 1000000000;
        for (int i = 0; i < arr.length; i++) {
            int id = binSearch(tree, arr, i, target);

            if (id < arr.length) {
                int and = query(tree, 1, 0, arr.length - 1, i, id);

                ans = Math.min(ans, Math.abs(and - target));
            }
            id--;
            if (id >= i) {
                int and = query(tree, 1, 0, arr.length - 1, i, id);
                ans = Math.min(ans, Math.abs(and - target));
            }
        }
        return ans;
    }

    /**
     * 返回第一个小于等于t的数（注意是递减的）
     */
    public int binSearch(int[] tree, int[] a, int start, int t) {
        int l = start, r = a.length;
        while (l <= r) {
            int mid = (l + r) >> 1;
            int and = query(tree, 1, 0, a.length, start, mid);
            if (and > t) {
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return l;
    }

    //建线段树
    public void build(int[] and, int treeNode, int[] a, int l, int r) {
        if (l == r) {
            and[treeNode] = a[l];
        } else {
            int mid = (l + r) >> 1;
            build(and, treeNode * 2, a, l, mid);
            build(and, treeNode * 2 + 1, a, mid + 1, r);
            and[treeNode] = and[treeNode * 2] & and[treeNode * 2 + 1];
        }
    }

    //查询线段树
    public int query(int sum[], int treeNode, int nodeL, int nodeR, int l, int r) {
        // System.out.println("x,y,curx,cury,curi = " + l + "," + r + "," + nodeL + "," + nodeR + "," + treeNode);
        if (nodeL >= l && nodeR <= r) {
            return sum[treeNode];
        }
        int ret = -1;
        int mid = (nodeL + nodeR) >> 1;
        if (l <= mid) {
            ret &= query(sum, treeNode * 2, nodeL, mid, l, r);
        }
        if (r > mid) {
            ret &= query(sum, treeNode * 2 + 1, mid + 1, nodeR, l, r);
        }
        return ret;
    }
```







## 思路2（官方原解）

**枚举右节点**

重复利用已经算过的值。



固定的右端点 r，按位与之和最多只有 2020 种不同的值，因此我们可以使用一个集合维护所有的值。

我们从小到大遍历r，并用一个集合实时地维护 func(arr,l,r) 的所有不同的值，集合的大小不过超过20。当我们从 
r 遍历到 r+1 时，以 r+1 为右端点的值，就是集合中的每个值和 arr[r+1] 进行按位与运算得到的值，再加上 arr[r+1] 本身。我们对这些新的值进行去重，就可以得到 func(arr,l,r+1) 对应的值的集合。

在遍历的过程中，当我们每次得到新的集合，就对集合中的每个值更新一次答案即可。



### 代码

```java

    public int closestToTarget(int[] arr, int target) {
        Set<Integer> st = new HashSet<>();
        //求解
        int ans = 1000000000;
        for (int i = 0; i < arr.length; i++) {
            Set<Integer> newSt = new HashSet<>();
            for (Integer v : st) {
                v &= arr[i];
                newSt.add(v);
            }
            newSt.add(arr[i]);
            for (Integer v : newSt) {
                ans = Math.min(ans, Math.abs(v - target));
            }
            st = newSt;
        }
        return ans;
    }
```






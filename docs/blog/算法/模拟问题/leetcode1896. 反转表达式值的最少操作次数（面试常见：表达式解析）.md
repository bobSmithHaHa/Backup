

## 题目

难度系数：Hard




## 题意

给你一个 有效的 布尔表达式，用字符串 expression 表示。这个字符串包含字符 '1'，'0'，'&'（按位 与 运算），'|'（按位 或 运算），'(' 和 ')' 。

比方说，"()1|1" 和 "(1)&()" 不是有效 布尔表达式。而 "1"， "(((1))|(0))" 和 "1|(0&(1))" 是 有效 布尔表达式。
你的目标是将布尔表达式的 值 反转 （也就是将 0 变为 1 ，或者将 1 变为 0），请你返回达成目标需要的 最少操作 次数。

比方说，如果表达式 expression = "1|1|(0&0)&1" ，它的 值 为 1|1|(0&0)&1 = 1|1|0&1 = 1|0&1 = 1&1 = 1 。我们想要执行操作将 新的 表达式的值变成 0 。
可执行的 操作 如下：

将一个 '1' 变成一个 '0' 。
将一个 '0' 变成一个 '1' 。
将一个 '&' 变成一个 '|' 。
将一个 '|' 变成一个 '&' 。
注意：'&' 的 运算优先级 与 '|' 相同 。计算表达式时，括号优先级 最高 ，然后按照 从左到右 的顺序运算。

提示：

1 <= expression.length <= 105
expression 只包含 '1'，'0'，'&'，'|'，'(' 和 ')'
所有括号都有与之匹配的对应括号。
不会有空的括号（也就是说 "()" 不是 expression 的子字符串）。



来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/minimum-cost-to-change-the-final-value-of-expression
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。



## 思路1

建树并记忆化搜索（dp）



有效代码：163行！

**注意特殊样例：**

计算表达式时，括号优先级最高 ，然后按照 从左到右 的顺序运算。所以最后的运算为跟节点！



### 代码

```java
import java.util.Stack;

/**
 * leetcode 提交代码
 * 使用：
 * - 直接复制全部代码提交，包括import
 */
public class Solution {
    /**
     * 提交到oj后不会输出
     */
    private static void println(String s) {
        if (!isLocal) {
            return;
        }
        System.out.println("bf: " + s);
    }

    public static boolean isLocal = false;
    private static final int MOD = (int)1e9 + 7;
    private static final double EPS = 1e-8;

    //

    public static class Node {
        char c;
        int ans, d = -1;
        Node l, r;

        public void getAns() {
            if (c == '&') {
                this.ans = l.ans & r.ans;
            } else {
                this.ans = l.ans | r.ans;
            }
        }

        public boolean isLeaf() {
            return l == null && r == null;
        }

        @Override
        public String toString() {
            return "Node(" + "c=" + c + ", ans=" + ans + ", d=" + d + ')';
        }
    }

    /**
     * 建树时最后算的在根节点，即右边的最后算（ERROE）
     */
    public Node build(String exp, int l, int r, int[] f) {
        char c = exp.charAt(r);
        if (l == r) {
            Node node = new Node();
            node.c = c;
            node.ans = c - '0';
            return node;
        }
        if (c == '0' || c == '1') {
            return newNode(exp, l, r, r - 1, f);
        }
        // 最后是括号
        if (c == ')') {
            // 前括号的位置
            int id = f[r];
            // 整个表达式在()中
            if (id == l) {
                return build(exp, l + 1, r - 1, f);
            }
            return newNode(exp, l, r, id - 1, f);
        }
        return null;
    }

    /**
     * 分解表达式建结点
     */
    public Node newNode(String exp, int l, int r, int id, int[] f) {
        Node node = new Node();
        node.c = exp.charAt(id);
        node.l = build(exp, l, id - 1, f);
        node.r = build(exp, id + 1, r, f);
        node.getAns();
        return node;
    }

    public void dfs(Node r) {
        if (r.d != -1) {
            return;
        }
        if (r.isLeaf()) {
            r.d = 1;
            return;
        }
        dfs(r.l);
        dfs(r.r);
        if (r.c == '&') {
            // 原来都是1->0
            if (r.ans == 1) {
                // 变数字：把一个1变成0
                r.d = Math.min(r.l.d, r.r.d);
                // 变符号为|：需要都变成0
                r.d = Math.min(r.d, r.l.d + r.r.d + 1);
            } else {// 0->1
                //变数字：原来至少一个0，把0都变成1
                r.d = 0;
                if (r.l.ans == 0) {
                    r.d += r.l.d;
                }
                if (r.r.ans == 0) {
                    r.d += r.r.d;
                }
                // 变符号为|：需要有一个1
                if (r.l.ans == 1 || r.r.ans == 1) {
                    r.d = 1;
                } else { // 都是0
                    r.d = Math.min(r.d, r.l.d + 1);
                    r.d = Math.min(r.d, r.r.d + 1);
                }
            }
        } else { // 符号：|
            // 原来都是0
            if (r.ans == 0) {
                // 变数字：把一个0变成1
                r.d = Math.min(r.l.d, r.r.d);
                // 变符号为&：需要都变1
                r.d = Math.min(r.d, r.l.d + r.r.d + 1);
            } else { // 1 //原来至少一个1
                // 变数字：把1都变成0
                r.d = 0;
                if (r.l.ans == 1) {
                    r.d += r.l.d;
                }
                if (r.r.ans == 1) {
                    r.d += r.r.d;
                }
                // 变符号为&：需要有一个0
                if (r.l.ans == 0 || r.r.ans == 0) {
                    r.d = 1;
                } else { // 都是1
                    r.d = Math.min(r.d, r.l.d + 1);
                    r.d = Math.min(r.d, r.r.d + 1);
                }
            }
        }
    }

    public void printTree(Node node) {
        if (node.l != null) {
            System.out.print("{");
            printTree(node.l);
            System.out.print("}");
        }
        System.out.print(node);

        if (node.r != null) {
            System.out.print("{");
            printTree(node.r);
            System.out.print("}");
        }
    }

    public int minOperationsToFlip(String exp) {
        int id = 0;
        int n = exp.length();
        int[] f = new int[n];
        // 预处理找到对应的括号，而不是每次去求（ERROR：超时）
        Stack<Integer> st = new Stack<>();
        for (int i = 0; i < n; i++) {
            char c = exp.charAt(i);
            if (c == '(') {
                st.push(i);
            } else if (c == ')') {
                f[i] = st.pop();
            }
        }
        Node root = build(exp, 0, n - 1, f);

        dfs(root);

        // printTree(root);
        // System.out.println();

        return root.d;
    }
}

```





## 思路2

不需要建树直接表达式解析递归就可以！



存放一个二元组 (𝑥,𝑦)，其中 𝑥 表示将对应表达式的值变为 0,需要的最少操作次数，y 表示将对应表达式的值变为 1，需要的最少操作次数。



对应的处理方式：
1.如果是数字那么返回将数字改为0最少需要多少次操作，改为1最少需要多少次操作。
2.如果是表达式，那么递归地求表达式1改为0和1需要的操作以及表达式2改为0和1需要的操作,那么整个表达式的结果修改为0需要的操作为：
min(两边都为0的操作数，有一边为0且将运算符修改为&的操作数)
将整个表达式结果修改为1的操作为：
min(两边都为1的操作数，有一边为1且将运算符修改为|的操作数)



最后取修改为0和1需要的较大值为最终结果，因为表达式的值不修改的话一定为0或者1，需要0次操作，如果要逆转结果肯定需要操作，所以取大值。

**注意特殊样例：**



### 代码

```java
import java.util.Stack;

/**
 * leetcode 提交代码
 * 使用：
 * - 直接复制全部代码提交，包括import
 */
public class Solution {
    /**
     * 提交到oj后不会输出
     */
    private static void println(String s) {
        if (!isLocal) {
            return;
        }
        System.out.println("bf: " + s);
    }

    public static boolean isLocal = false;
    private static final int MOD = (int)1e9 + 7;
    private static final double EPS = 1e-8;

    //

    /**
     * 右边的最后算（ERROE）
     */
    public int[] dfs(String exp, int l, int r, int[] f) {
        char c = exp.charAt(r);
        if (l == r) {
            if (c == '0') {
                return new int[] {0, 1};
            } else {
                return new int[] {1, 0};
            }
        }
        // 符号的位置
        int mid = r - 1;
        if (c == '0' || c == '1') {
            mid = r - 1;
        }
        // 最后是括号
        if (c == ')') {
            // 前括号的位置
            int id = f[r];
            // 整个表达式在()中
            if (id == l) {
                return dfs(exp, l + 1, r - 1, f);
            }
            mid = f[r] - 1;
        }
        char fu = exp.charAt(mid);
        int[] lma = dfs(exp, l, mid - 1, f);
        int[] rma = dfs(exp, mid + 1, r, f);
        int zero = Math.min(lma[0] + rma[0],
            Math.min(lma[0] + rma[1] + (fu == '&' ? 0 : 1), lma[1] + rma[0] + (fu == '&' ? 0 : 1)));

        int one = Math.min(lma[1] + rma[1],
            Math.min(lma[0] + rma[1] + (fu == '|' ? 0 : 1), lma[1] + rma[0] + (fu == '|' ? 0 : 1)));

        return new int[] {zero, one};
    }

    public int minOperationsToFlip(String exp) {
        int id = 0;
        int n = exp.length();
        int[] f = new int[n];
        // 预处理找到对应的括号，而不是每次去求（ERROR：超时）
        Stack<Integer> st = new Stack<>();
        for (int i = 0; i < n; i++) {
            char c = exp.charAt(i);
            if (c == '(') {
                st.push(i);
            } else if (c == ')') {
                f[i] = st.pop();
            }
        }
        int[] ans = dfs(exp, 0, n - 1, f);

        return Math.max(ans[0], ans[1]);
    }
}

```








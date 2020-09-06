

难度系数：Hard





## 题源

Leetcode：[String Compression II](https://leetcode.com/contest/weekly-contest-199/problems/string-compression-ii/)  



## 题意

行程长度编码 是一种常用的字符串压缩方法，将连续的相同字符（重复 2 次或更多次）替换为字符和表示字符计数的数字（行程长度）。例如，压缩字符串 "aabccc" ，将 "aa" 替换为 "a2" ，"ccc" 替换为` "c3" 。压缩后的字符串变为 "a2bc3" 。

注意，本问题中，压缩时没有在单个的字符后附加 '1' 。



给你一个字符串 s 和一个整数 k 。你需要从字符串 s 中删除最多 k 个字符，以使 s 的行程长度编码最小。

请你返回删除最多 k 个字符后，s 行程长度编码的最小长度 。



## 思路

### 自己开始思路



`d[i][k][last][num]`: 表示字母串前i个数、最多删除k个、最后字母一个是last、最后字母连续出现num次的最小值

求`d[i][k][last][num]`的思路是固定last，判断`s[i]`是不是等于last。

状态变化

- if `s[i]` != last
  - 必须删除第i个字母，变为求`d[i-1][k-1][last][num]`
- else
  - 保留第i个字母，求`d[i-1][k][last][num-1]+（num是否变长？1：0）`，
    - 如果num-1==0，需要枚举last，同时需要枚举num？？？（**ERROR：太复杂且超时**）



**这个思路之所以不行的原因：**

**DP的方向错误：dp的方向是n -> 0（从右向左，左边未知），但是last和num字段却是依赖左侧的。**



### 修改后的思路

**<u>改变DP方向后豁然开朗。</u>**

`d[i][k][last][num]`: 表示字母串从i到n-1的、最多删除k个、上一个字母一个是last、last连续出现num次的最小值。

从dp(0,k,null,0)开始dp，

状态变化

- 删除第i个字母
  - 变为求`d[i+1][k-1][last][num]`
- 不删除第i个字母
  - if `s[i]` == last
    - 变为求`d[i+1][k][last][num+1]+（num是否变长？1：0）`
  - else
    - 变为求`d[i+1][k][s[i]][1]+1`



#### 代码

```java

    public int getLengthOfOptimalCompression(String s, int k) {
        int n = s.length();
        int[][][][] d = new int[n][k + 1][27][101];
      	// 初始化
        for (int i = 0; i < n; i++) {
            for (int j = 0; j <= k; j++) {
                for (int l = 0; l < 27; l++) {
                    Arrays.fill(d[i][j][l], -1);
                }
            }
        }
        return f(d, 0, k, 26, 0, s);
    }

    public int f(int[][][][] d, int i, int k, int last, int num, String s) {
        if (k < 0) {
            return Integer.MAX_VALUE / 2;
        }
        if (i == s.length()) {
            return 0;
        }
      	// 之前计算过
        if (d[i][k][last][num] != -1) {
            return d[i][k][last][num];
        }
        int ans = Integer.MAX_VALUE / 2;
        // 删除当前字符
        ans = f(d, i + 1, k - 1, last, num, s);

        // 不删除当前字符
        if (last == toInt(s, i)) {
            ans = Math.min(ans, f(d, i + 1, k, last, num + 1, s) + concat(num + 1));
        } else {
            ans = Math.min(ans, f(d, i + 1, k, toInt(s, i), 1, s) + 1);
        }

        return d[i][k][last][num] = ans;

    }

    /**
     * 当某个字母连续出现2、10、100词的时候，个数需要加一
     *
     * @param curCount
     * @return
     */
    public int concat(int curCount) {
        return (curCount == 2 || curCount == 10 || curCount == 100) ? 1 : 0;
    }

    public int toInt(String s, int i) {
        return s.charAt(i) - 'a';
    }
```





### 优化的思路

**<u>分组DP</u>**

上面的思路最复杂的地方在于：需要记住之前的状态last和num。

**思路是：去掉记录之前的状态,相同字母分成紧密一组，做相同处理。**

`d[i][k]`：表示字母串从i到n-1的、最多删除k个的最小值。

从dp(0,k)开始dp，



状态变化：

- 删除第i个字母
  - 变为求`d[i+1][k-1]`
- 不删除第i个字母
  - 找到j，使得`s[i]和s[j]之间不等于s[i]的都删掉后的最大j（长度小于等于k）` 
  - 变为求`(j-i+1)的编码长度+d[j+1][k-(j-i+1)]`



#### 代码

```java

    public int getLengthOfOptimalCompression(String s, int k) {
        int n = s.length();
        int[][] d = new int[n][101];
        for (int i = 0; i < n; i++) {
            Arrays.fill(d[i], -1);
        }
        return f(d, 0, k, s);
    }

    public int f(int[][] d, int i, int k, String s) {
        if (k < 0) {
            return Integer.MAX_VALUE / 2;
        }
        //剪枝
        if (i + k >= s.length()) {
            return 0;
        }
        if (d[i][k] != -1) {
            return d[i][k];
        }
        //删除第i个字母
        d[i][k] = f(d, i + 1, k - 1, s);

        //j从i开始算而不是i+1，
        int diff = 0, same = 0, j = i;
        for (; j < s.length() && diff <= k; j++) {
            if (s.charAt(i) == s.charAt(j)) {
                same++;
            } else {
                diff++;
            }
            d[i][k] = Math.min(d[i][k], f(d, j + 1, k - diff, s) + encode((same)));
        }
        return d[i][k];
    }

    /**
     * 相同字母的编码
     * @param curCount
     * @return
     */
    public int encode(int curCount) {
        return (curCount < 2) ? 1 : (curCount < 10 ? 2 : (curCount < 100 ? 3 : 4));
    }
```










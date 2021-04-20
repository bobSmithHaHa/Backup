

## 题源

Leetcode：https://leetcode-cn.com/problems/maximum-number-of-non-overlapping-substrings/

难度系数：Hard


## 题意

给你一个只包含小写字母的字符串 s ，你需要找到 s 中最多数目的非空子字符串，满足如下条件：

这些字符串之间互不重叠，也就是说对于任意两个子字符串 s[i..j] 和 s[k..l] ，要么 j < k 要么 i > l 。
如果一个子字符串包含字符 char ，那么 s 中所有 char 字符都应该在这个子字符串中。



请你找到满足上述条件的最多子字符串数目。如果有多个解法有相同的子字符串数目，请返回这些子字符串总长度最小的一个解。可以证明最小总长度解是唯一的。请注意，你可以以 任意 顺序返回最优解的子字符串。



示例 1：

输入：s = "adefaddaccc"
输出：["e","f","ccc"]
解释：下面为所有满足第二个条件的子字符串：
[
  "adefaddaccc"
  "adefadda",
  "ef",
  "e",
  "f",
  "ccc",
]
如果我们选择第一个字符串，那么我们无法再选择其他任何字符串，所以答案为 1 。如果我们选择 "adefadda" ，剩下子字符串中我们只可以选择 "ccc" ，它是唯一不重叠的子字符串，所以答案为 2 。同时我们可以发现，选择 "ef" 不是最优的，因为它可以被拆分成 2 个子字符串。所以最优解是选择 ["e","f","ccc"] ，答案为 3 。不存在别的相同数目子字符串解。
示例 2：

输入：s = "abbaccd"
输出：["d","bb","cc"]
解释：注意到解 ["d","abba","cc"] 答案也为 3 ，但它不是最优解，因为它的总长度更长。

1 <= s.length <= 10^5
s 只包含小写英文字母。









## 思路

转化为**贪心**求最多区间选择问题。每个子字符串转化为[l,r]（左右端点）的区间。

## 注意特殊样例：

abababab、abababa、abefab、bbbb、



#### 代码

```java
    public List<String> maxNumOfSubstrings(String s) {
        List<String> ans = new ArrayList<>();
        List<java.util.AbstractMap.SimpleEntry<Integer, Integer>> list = new ArrayList<>();
        //求每个字符的最左和最右点
        int[][] posi = new int[26][2];
        for (int i = 0; i < 26; i++) {
            int l = -1, r = -1;
            for (int j = 0; j < s.length(); j++) {
                if (s.charAt(j) == 'a' + i) {
                    l = l == -1 ? j : l;
                    r = j;
                }
            }
            posi[i][0] = l;
            posi[i][1] = r;
        }
        //遍历求子字符串的区间
        int[] vis = new int[26];
        for (int i = 0; i < s.length(); i++) {
            int l = i, r = i, ok = 1;
            for (int j = i; j <= r; j++) {
                int c = s.charAt(j) - 'a';
                if (vis[c] == 1) {
                    ok = 0;
                    break;
                }
                r = Math.max(r, posi[c][1]);
            }
            vis[s.charAt(i) - 'a'] = 1;
            if (ok == 1) {
                java.util.AbstractMap.SimpleEntry entry = new java.util.AbstractMap.SimpleEntry<Integer, Integer>(l, r);
                list.add(entry);
            }
        }
        // System.out.println("list = " + list);
        //最多区间选择问题
        list.sort(Map.Entry.comparingByValue());
        int end = -1;
        for (java.util.AbstractMap.SimpleEntry<Integer, Integer> entry : list) {
            int l = entry.getKey(), r = entry.getValue();
            if (l > end) {
                end = r;
                ans.add(s.substring(l, r + 1));
            }
        }
        return ans;
    }

```










title: "最长公共子字符串与最长公共子序列"
date: 2013-03-20
tags: 算法
---

最长公共子字符串（longest common substring）与最长公共子序列（longest common subsequence）是不一样的，substring 要求字符连续，subsequence只要求字符顺序一样，不要求连续。关于这两个题目的解法，都说是采用动态规划算法，研究了半天，暂时还没研究透彻（人老迟钝了么？）。这里暂且记住这两个题目的解法。这个题目的解法思路大致相同，都是构造一个二维数组（XY的位置分别对应两个字符串的索引的位置），根据字母的对应关系，在矩阵的相应位置上构造数字。构造完成后，再从最大的数字回朔，然后找到相应的串。

## 最长公共子字符串

先看最长公共子字符串的解法，根据2个字符串的长度构造一个二维数组a[][]，按照下面条件对数组个位置构造数值。

若s1[i]!=s2[j]，则a[i][j]=0
若s1[i]==s2[j]，则a[i][j]=a[i-1][j-1]+1
就拿abcdefg和xcdef来举例，构造出的数组如下：

```
  a b c d e f g
x 0 0 0 0 0 0 0
c 0 0 1 0 0 0 0
d 0 0 0 2 0 0 0
e 0 0 0 0 3 0 0
f 0 0 0 0 0 4 0
```

下面是java实现代码：

``` java
public class LongestCommonSubstring {
    public String search(String s1, String s2) {
        if (s1 == null || s1.isEmpty() || s2 == null || s2.isEmpty()) {
            return "";
        }
 
        int len1 = s1.length();
        int len2 = s2.length();
 
        int[][] match = new int[len1][len2];
        int maxLength = 0; // 子字符串的最大长度
        int lastIndex = 0; // 最大子字符串中最后一个字符的索引
 
        for (int i = 0; i < len1; i++) {
            for (int j = 0; j < len2; j++) {
 
                if (s2.charAt(j) == s1.charAt(i)) {
                    if (i > 0 && j > 0 && match[i-1][j-1] != 0) {
                        match[i][j] = match[i-1][j-1] + 1;
                    } else {
                        match[i][j] = 1;
                    }
 
                    if (match[i][j] > maxLength) {
                        maxLength = match[i][j];
                        lastIndex = i;
                    }
                } else {
                    match[i][j] = 0;
                }               
            }
        }
 
        // 这里打印出构造出的矩阵
        for (int i = 0; i < len1; i++) {
            for (int j = 0; j < len2; j++) {
                System.out.print(match[i][j] + " ");
            }
            System.out.println();
        }
 
        if (maxLength == 0) {
            return "";
        }
 
        StringBuilder sb = new StringBuilder();
        // 根据最大索引的位置，回朔出最长子字符串
        for (int i = lastIndex-maxLength+1; i <= lastIndex; i++) {
            sb.append(s1.charAt(i));
        }
 
        return sb.toString();
    }
 
    public static void main(String[] args) {
        String s1 = "xcdef";
        String s2 = "abcdefg";
 
        System.out.println(new LongestCommonSubstring().search(s1, s2));
    }
}
```

## 最长公共子序列

这是来自维基百科的解法：
以两个序列 X、Y 为例子，设有二维数组 f[i,j] 表示 X 的 i 位和 Y 的 j 位之前的最长公共子序列的长度，则有：
f[1][1] = same(1,1);
f[i,j] = max{f[i-1][j -1] + same(i,j),f[i-1,j],f[i,j-1]}
其中，same(a,b)当 X 的第 a 位与 Y 的第 b 位完全相同时为“1”，否则为“0”。
此时，f[j]中最大的数便是 X 和 Y 的最长公共子序列的长度，依据该数组回溯，便可找出最长公共子序列。

用Java实现代码如下：

``` java
public String search(String s1, String s2) {
    if (s1 == null || s1.isEmpty() || s2 == null || s2.isEmpty()) {
        return "";
    }
 
    int[][] tmp = new int[s1.length() + 1][s2.length() + 1];
 
    for (int i = 1; i <= s1.length(); i++) {
        for (int j = 1; j <= s2.length(); j++) {
            if (s1.charAt(i - 1) == s2.charAt(j - 1)) {
                tmp[i][j] = tmp[i - 1][j - 1] + 1;
            } else {
                tmp[i][j] = Math.max(tmp[i - 1][j], tmp[i][j - 1]);
            }
        }
    }
 
    StringBuilder sb = new StringBuilder();
 
    int i = s1.length(), j = s2.length();
 
    while (i > 0 && j > 0) {
        if (s1.charAt(i - 1) == s2.charAt(j - 1)) {
            sb.append(s1.charAt(i - 1));
            i--;
            j--;
        } else if (tmp[i][j - 1] >= tmp[i - 1][j]) {
            j--;
        } else {
            i--;
        }
    }
 
    return sb.reverse().toString();
}
```
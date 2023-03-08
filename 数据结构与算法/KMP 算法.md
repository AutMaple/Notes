# KMP 算法

KMP 是著名的字符串匹配算法，该算法效率很高，但确实也是很复杂，时间复杂度为 $O(N)$ ，空间复杂度为 $O(M)$， 其中 N 为样本字符串的长度，M 为模式字符串的长度。即使用 KMP 算法只需要遍历字符串一次即可找出全部匹配的字符串

在介绍 KMP 算法之前，得先了解如下两个概念：

- 前缀: 指不包含最后一个字符的所有以第一个字符开头的连续子串
- 后缀: 指不包含第一个字符的所有以最后一个字符结尾的连续子串
- 模式串: 需要在文本串中查找的子串
- 文本串:  待查找的字符串
- 前缀表: 记录下标i之前(包括 i )的字符串中，有多大长度的相同前缀后缀

KMP 算法就是要在文本串中查找模式串。KMP 算法的核心是：遍历 **文本串** 的指针 **只进不退**，并通过模式串的前缀与文本串的后缀的比较，将 **模式串** 的指针修改到正确的位置上。
- 如果前缀和后缀相等，则将模式串的指针移动到模式串的 **Length(前缀)** 的位置上
- 如果前缀和后缀不相等，则将模式串的指针移动到模式串开头的位置。

## Next 数组的生成

Next 数组中存放的是模式串当前字符的最大公共前后缀长度。当字符串匹配失败之后，可以通过 Next 数组快速定位模式串下一个指针开始的位置，而不需要每次都重头开始匹配。

模式串确定之后，Next 数组也就确定了，即需要通过模式串来生成 Next 数组。Next 数组的长度为 **Length(模式串)**。`Next[i]` 存放的是模式串子串 `0 - i` 的最大公共前后缀长度。

生成 Next 数组的代码：

```java
public static int[] initNext(String pattern) {  
    int len = pattern.length();  
    int[] next = new int[len];  
    // 指向需要匹配的前一个字符, j 既是最大相同前后缀的长度 - 1，也代表模式串中下一个待匹配字符的前一个字符的指针  
    int j = -1;
    next[0] = -1;  
    for (int i = 1; i < len; i++) {  
        while (j >= 0 && pattern.charAt(j + 1) != pattern.charAt(i))  
            j = next[j];  
        if (pattern.charAt(i) == pattern.charAt(j + 1))  
            j++;  
        next[i] = j;  
    }  
    return next;  
}
```

上述代码中的变量 `j` 是模式串中下一个待匹配字符的前一个字符的指针

`abacabab` 模式字符串的 Next 数组为: `[-1, -1, 0, -1, 0, 1, 2, 1]`

Next 数组中存放的值既是模式串匹配失败之后下一个待匹配字符的前一个字符的指针，也是最大公共前后缀的长度值 - 1.

- `-1` 表示当前子串的最大公共前后缀长度 0； `0` 表示最大公共前后缀长度为 1
- `-1` 表示匹配失败之后，从模式串的第 `0` 个位置开始匹配；`0` 表示从从模式串的第 `1` 个位置开始匹配

变量 `j` 也可以是指向模式串中下一个待匹配字符的指针：

```java
public static int[] next(String s) {  
    int len = s.length();  
    int[] next = new int[len];  
    next[0] = 0;  
    int j = 0;  
    for (int i = 1; i < len; i++) {  
        while (j > 0 && s.charAt(i) != s.charAt(j))  
            j = next[j - 1];  
        if (s.charAt(i) == s.charAt(j))  
            j++;
        next[i] = j;
    }
    return next;  
}
```


# 代码实现

```java
package KMP;  
  
import java.util.Arrays;  
  
public class Solution {  
    public static int search(String txt, String pattern) {  
        if (pattern.length() == 0)  
            return 0;  
        int[] next = initNext(pattern);  
        int j = -1;  
        for (int i = 0; i < txt.length(); i++) {  
            while (j >= 0 && txt.charAt(i) != pattern.charAt(j + 1)) {  
                j = next[j];  
            }  
            if (txt.charAt(i) == pattern.charAt(j + 1))  
                j++;  
            if (j == pattern.length() - 1)  
                return i - pattern.length() + 1;  
        }  
        return -1;  
    }  
  
    /**  
     * a  c  a  b a c a c     * -1 -1  0 -1 0 1 2 1     * a  b  a     * -1 -1  0     * <p>  
     * 构建 next 数组的过程，其实也是一个字符串匹配的问题  
     * 对于 acabacac 这个模式字符串来说，某个字符的最大相同前后缀需要通过其前一个字符的来进行判定  
     */  
    public static int[] initNext(String pattern) {  
        int len = pattern.length();  
        int[] next = new int[len];  
        // 指向需要匹配的前一个字符, j 既是最大相同前后缀的长度 - 1，也代表模式串中下一个待匹配字符的前一个字符的指针  
        int j = -1;  
        next[0] = -1;  
        for (int i = 1; i < len; i++) {  
            // 如果当前字符不相等，就对回退到前一个  
            while (j >= 0 && pattern.charAt(j + 1) != pattern.charAt(i))  
                j = next[j];  
            if (pattern.charAt(i) == pattern.charAt(j + 1))  
                j++;  
            next[i] = j;  
        }  
        return next;  
    }  
  
    public static void main(String[] args) {  
        int index = search("acabacac", "abac");  
        System.out.println(index);  
        int[] next = initNext("abacabab");  
        System.out.println(Arrays.toString(next));  
    }  
}
```
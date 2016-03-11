title: "我所知道的正则表达式（2）- 贪婪模式与懒惰模式"
date: 2013-01-15
tags: 正则表达式
categories: 技术
---

了解了正则表达式基础知识已经能够能应付大多数情况，像字符串匹配、正则替换、校验等；但是再使用的过程中你可能发现一个问题，就是当使用重复元字符匹配数量时，总是会尽可能长的去匹配，而有时这恰恰不是你想要的。看下面的例子：

``` html
<a href=”http://www.google.com”>谷歌</a><a href=”http://www.baidu.com”>百度</a>
```

你希望匹配出每个链接的html内容。如果你用 &lt;a (.*)&lt;/a>会首先匹配到整个字符串，而不是你希望的&lt;a href=”http://www.google.com”>谷歌&lt;/a>和&lt;a href=”http://www.baidu.com”>百度&lt;/a>。

为什么会出现这种情况呢？这就是正则表达式的贪婪模式。当出现重复数量的时候，会尽可能的多匹配。上述的正则表达式中 . 表示任意字符，* 代表可以重复出现任意个，根据正则表达式的贪婪个性，不匹配到最后才怪呢。就像人一样，有贪婪就有懒惰，一个?就可以让正则立刻改变本性，这时的正则就表现懒惰模式的本性了。

修改后的正则表达式为：&lt;a (.*?)&lt;/a>，这时就可以匹配出每个链接的html了。

测试用例为：

``` java
import java.util.regex.Matcher;
import java.util.regex.Pattern;
 
public class Test {
    public static void main(String[] args) throws Exception {
        String value = "<a href="http://www.google.com">谷歌</a><a href="http://www.baidu.com">百度</a>";
 
        System.out.println("贪婪模式：");
        Pattern pattern = Pattern.compile("<a (.*)</a>");
        Matcher matcher = pattern.matcher(value);
        while (matcher.find()) {
            System.out.println(matcher.group(0));
        }
 
        System.out.println("贪婪模式：");
        pattern = Pattern.compile("<a (.*?)</a>");
        matcher = pattern.matcher(value);
        while (matcher.find()) {
            System.out.println(matcher.group(0));
        }
    }
}
```

最后总结一下数量元字符懒惰模式的常用写法(其实就是多了一个?)：

<table><tr><td>x*?</td><td>0个或者任意个x, 最少匹配</td></tr><tr><td>x??</td><td>0个或这1个x, 最少匹配</td></tr><tr><td>x+?</td><td>至少1个x, 最少匹配</td></tr><tr><td>x{n,}?</td><td>至少n个x, 最少匹配</td></tr><tr><td>x{n,m}?</td><td>至少n个x，至多m个x, 最少匹配</td></tr></table>
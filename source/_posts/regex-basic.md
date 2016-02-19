title: "我所知道的正则表达式（1）- 基础知识"
date: 2013-01-11
tags: 正则表达式
---

正则表达式是处理文本时的有力工具，能够按照一定的模式或规则去匹配具有一定格式的文本，并可以完成分组、替换等复杂功能。作为一程序员，正则作为必备武器，能给我们处理文带来极大的便利。经常用到的正则的场景如：javascript验证输入格式（密码、电话、email等）、网页爬虫从网页中的解析出需要的内容，替换相同模式的文本内容等。<!--more-->

正则中预定义了一些元字符，用来表示字符、个数、重复次数、逻辑关系等，以下是大致介绍：

## 字符匹配

<table><tbody><tr><td>.</td><td>匹配任意一个字符</td></tr><tr><td>^</td><td>行开头</td></tr><tr><td>$</td><td>行结束</td></tr><tr><td>\</td><td>转义符</td></tr><tr><td>\d</td><td>一个数字，相当于[0-9]</td></tr><tr><td>\s</td><td>一个空白字符，相当于[ \t\n\x0B\f\r]</td></tr><tr><td>\w</td><td>一个单词中的字符:字母、数字、下划线，相当于[a-zA-Z_0-9]</td></tr><tr><td>\b</td><td>单词的边界（单词的开头或结尾）</td></tr><tr><td>[]</td><td>[]里面的任意一个字符</td></tr></tbody></table>

## 数量匹配，用来匹配字符出现的次数

<table border="1"><tbody><tr><td>x*</td><td>0个或者任意个x,</td></tr><tr><td>x?</td><td>0个或这1个x</td></tr><tr><td>x+</td><td>至少1个x</td></tr><tr><td>x{n}</td><td>n个x</td></tr><tr><td>x{n,}</td><td>至少n个x</td></tr><tr><td>x{n,m}</td><td>至少n个x，至多m个x</td></tr></tbody></table>

注:此为贪婪模式，就是匹配最多的结果。例如有个字符串abcdefg#aaa, [a-z]+的第一次匹配结果为abcdefg，不会是a或者ab等

## []的使用举例

<table border="1"><tbody><tr><td>[0-9]</td><td>一个数字</td></tr><tr><td>[^0-9]</td><td>一个非数字字符</td></tr><tr><td>[a-z]</td><td>a-z中的任意一个字母（所有小写字母）</td></tr><tr><td>[A-Z]</td><td>A-Z中的任意一个字母（所有大写字母）</td></tr><tr><td>[a-zA-Z]</td><td>所有字母的一个</td></tr><tr><td>[a-zA-Z0-9]</td><td>所有字母和数字中的一个</td></tr><tr><td>[a-ce-z]</td><td>a-c和e-z中的一个，不含d</td></tr><tr><td>[a-d[m-p]]</td><td>a-d或者m-p中的一个，这个不是所有解释器都支持</td></tr></tbody></table>

注：在[]中的^不是表示行开头，而是标识反义，意为“除xxx之外的所有”

## 逻辑关系

<table border="1"><tbody><tr><td>XY</td><td>X后面是Y</td></tr><tr><td>X|Y</td><td>X或者Y</td></tr></tbody></table>

## 分组

在一个正则表达式中，将局部用()包含成子表达式，目的是对子表达式进行重复匹配等操作，每个()就是一个组。在匹配的过程中，会将匹配每个子表达式字符按顺序保存在临时变量中，最多可保存9个分组，可以使用1到9来引用。

例如有这样文本，要求匹配出网站名称和链接：

```xml
<a href="http://www.google.com">谷歌</a><a href="http://www.baidu.com">百度</a>
```

就可以使用分组，正则为：<a href=”([^”]+)”>([^<]+)  ，每个括号为一组，共2个分组。

看下面的Java的测试用例：

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;
 
public class Test { 
    public static void main(String[] args) throws Exception {
        String value = "<a href="http://www.google.com">谷歌</a><a href="http://www.baidu.com">百度</a>";
        Pattern pattern = Pattern.compile("href="([^"]+)">([^<]+)");
        Matcher matcher = pattern.matcher(value);
 
        while (matcher.find()) {
            System.out.println(matcher.group(2) + " - " + matcher.group(1));
        }
    }
}
```


最终输出：
谷歌 - http://www.google.com
百度 - http://www.baidu.com

## URL重写

分组可以很方便的完成替换，例如在apache的urlrewrite模块中，经常用到如下的正则：

RewriteRule ^/article/([0-9]+)$ /article.php?id=$1 [L]

就是通过分组1获取到id，从而通过$1赋值到后面的url中去

## 不捕获分组和命名分组

<table border="1"><tbody><tr><td>(?:group-regex)</td><td>按分组(group-regex)来匹配，但不捕获该分组</td></tr><tr><td>(?&lt;name&gt;group-regex)</td><td>命名分组为name, 可以通过此名字捕获该分组</td></tr></tbody></table>

关于命名分组各个解析器有些不同， 上面是 java 解析器里的语法，而 Python 则是 (?P&lt;name&gt;group-regex)

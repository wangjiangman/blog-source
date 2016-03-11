title: "我所知道的正则表达式（3）- 零宽断言"
date: 2013-01-16
tags: 正则表达式
categories: 技术
---

正则表达式里面比较高级的应用就属于零宽断言了。那么什么是零宽断言呢？拆分法从字面上分析一下，零宽，即宽度为0，意味者不会返回匹配的字符，以为匹配的是当前字符的位置。断言，就是预言、假设，意味着从此处假设存在什么情况。那么零宽断言的意思就是假定从此位置开始满足某种情况。<!--more-->

根据断言字符串位于当前位置的前后关系，分为正向和反向断言，根据断言肯定和否定的语气，又有正向否定断言和反向否定断言。肯定即断言存在该字符串、否定即相反的意思：存在的不是该字符串，总之概念比较绕口，下表介绍的时候顺便给出英文：

<table><tbody><tr><td>(?=X)</td><td>正向断言，假定该位置后跟的是Xzero-width positive lookahead</td></tr><tr><td>(?!X)</td><td>正向否定断言，假设该位置后跟的不是Xzero-width negative lookahead</td></tr><tr><td>(?&lt;=X)</td><td>反向断言，假设该位置前跟的是Xzero-width positive lookbehind</td></tr><tr><td>(?&lt;!X)</td><td>反向否定断言，假设该位置前跟的不是Xzero-width negative lookbehind</td></tr></tbody></table>

举例

**(?=X) 正向断言**
[^\s]+?(?=ing) 来匹配 having doing listing，会匹配出 hav, do, list，注意：并不会匹配出ing，因为ing是零宽断言的部分。

**(?!X) 正向否定断言**
这个可参考以前的文章：一个匹配数字和字母密码的正则表达式

**(?&lt;=X) 反向断言**
(?&lt;=hell)[a-z]+ 来匹配test hellen hellas helloween，会匹配出 en, as, oween

<strong>(?&lt;!X) 反向否定断言</strong>
[a-z]+(?&lt;!hell)en 来匹配testen hellen hellas helloween，会匹配出testen和helloween

**注：所有的案例都在UE下进行测试。**
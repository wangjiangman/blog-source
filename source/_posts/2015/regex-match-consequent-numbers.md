title: "正则匹配连续数字"
date: 2015-11-13
tags: [正则表达式]
categories: 技术
---

今天有个网友问了匹配连续数字的正则，刚好无事，就帮忙写了一下，算是当作温习一下吧。<!--more-->

要求如下：
- 纯数字
- 5-7位之间
- 前三位相同
- 从第四位开始连续

如下面的例子：

```shell
11123       #正确
22234       #正确
33345       #正确
333456      #正确
2223456     #正确
0001234     #正确
00012345    #错误：此行长度超标
000234      #错误：第3位与第4位不连续
111235      #错误：第5位和第6位不连续
```

**1, 匹配三位相同数字**

```regex
(\d)\1{2}
```

**2, 匹配连续数字**

匹配连续数字可以通过零宽断言来匹配，当然也没什么好办法，只能通过列举，如下两行都可以匹配3位连续数字

```regex
(0(?=1)|1(?=2)|2(?=3)|3(?=4)|4(?=5)|5(?=6)|6(?=7)|7(?=8)|8(?=9)){2}\d
\d((?<=0)1|(?<=1)2|(?<=2)3|(?<=3)4|(?<=4)5|(?<=5)6|(?<=6)7|(?<=7)8|(?<=8)9){2}
```

说明： 第一行采用正向零宽断言，第二行采用反向零宽断言

**结论**

集合本题中的要求，由于要求相同前三，并且后面连续从第三位开始，完成正则如下：

```regex
^(\d)\1{2}((?<=(0(?=1)|1(?=2)|2(?=3)|3(?=4)|4(?=5)|5(?=6)|6(?=7)|7(?=8)|8(?=9)))\d){2,4}$
^(\d)\1{2}((?<=0)1|(?<=1)2|(?<=2)3|(?<=3)4|(?<=4)5|(?<=5)6|(?<=6)7|(?<=7)8|(?<=8)9){2,4}$
```

Done.

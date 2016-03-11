title: "innerHTML在ie,firefox,chrome的区别"
date: 2013-01-13
tags: 前端开发
categories: 技术
---

最近在用javascript 写一个代码语法高亮工具，由于取代码部分的时候需要用到innerHTML属性，发现了IE6, IE7, IE8与IE9, firefox, chrome对innerHTML实现的不同。现在IE9已经属于现代浏览器了，实现方式已经和firefox, chrome一致。下面说说区别：

IE6,IE7,IE8会将标签转为大写，对没有关闭的html标签（注意是html标签，比如<p>）进行关闭，并不在意其他的自定义标签(比如<xxxx>)是否关闭，并且会将属性值的引号也给丢掉了。

IE9,firefox,chrome会将标签专为小写，并关闭所有没有关闭的标签。

例如下面一段html代码，要求取div#test的innerHTML值：

``` html
<div id="test">
<span>you are here</span>
<XXXX>xxxx
<p>aaa
</div>
```

在IE9,firefox,chrome下取出的内容为：

``` html
<span>you are here</span>
<xxxx>xxxx
<p>aaa
</p></xxxx>
```

IE6,IE7,IE8下取出的内容为：

``` html
<SPAN class=a>you are here</SPAN>
<XXXX>xxxx
<P>aaa </P>
```

有时候仅仅是想取出里面的原始内容进行处理的话，还真是个麻烦事。
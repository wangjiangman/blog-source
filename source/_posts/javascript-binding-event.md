title: "利用javascript事件的特性高效的为子元素绑定操作"
date: 2013-03-11
tags: Web Front
categories: 技术
---

有这样一道javascript题目：在如下dom结构中，如何高效的给li元素绑定click事件，在用户单击li元素时能够提示li中的内容？

``` html
<ul> 
    <li>xxx</li> 
    <li>xxx</li> 
    <li>xxx</li> 
    <!-- 后续还有大量li元素 --> 
</ul>
```

最初想法就是遍历每个li元素，循环给li绑定onclick事件，如同下面的代码一样：

``` javascript
window.onload = function() {
    var ul = document.getElementById('ul'); 
 
    var lis = ul.getElementsByTagName('li');
    for (var i = lis.length-1; i >= 0; i--) {
        lis[i].onclick = function(e) {
            alert(this.innerHTML);
        }
    }
};
```

这种想法当然是最简单最直观也是正确的，但是存在一点问题，当dom中的li元素特别多的时候，这样循环遍历的绑定操作势必会站用大量的资源，这时候我们可以使用事件的一些特性，将操作绑定到li的父元素ul上面，当事件触发的时候，从event对象的属性中获取当前操作的对象，然后再完成其他的操作。如下面代码：

``` javascript
window.onload = function() {
    var ul = document.getElementById('ul');
 
    ul.onclick = function(e) {
        e = window.event ? window.event : e;
        var who = e.target ? e.target : e.srcElement;
        alert(who.innerHTML);
    };
};
```

这里我们用到了Event对象的target属性，该属性能够获取事件发生所在的元素。当然在IE下，该功能被srcElement代替。这种方式无疑是简练和高效的，除了照顾浏览器兼容多带来的几行代码外，这段代码多美妙啊。
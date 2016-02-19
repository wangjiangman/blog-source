title: "渐进增强实现首页弹出层登录"
date: 2013-01-12
tags: 前端开发
---

先说一下什么渐进增强。 渐进增强是前端开发的一个理念，对于前端开发，首先强调的是内容，然后在当前内容的基础上对功能进行增强。这并不是首先照顾那些低版本或者功能较差的浏览器（如移动浏览器)，因为在渐进增强的理念中甚至没有浏览器的概念。

与之对应的一个概念是“平稳退化“，这个开发理念主张首先针对高级的、现代的浏览器来开发设计网站，而对老的、落伍的浏览器提供一个仅仅可以工作的版本，却没有那么好的体验。

在我看来，这两种理念的思想本身差不多，出发点不太一样，最终都是要保持网站在不同境况带给用户的完整功能，只是有些体验差点，有些体验好些。

我这里会试着用这种前端开发理念，来实现一个首页弹出层登录的功能，我们可以先来探讨一下为什么要使用弹出层登录？我觉得弹出层会有如下2个好处（可能会有些肤浅）：

对用户来说，不用心打开一个页面，节省下载时间；带给用户更好的体验；
对网站提供者来说，至少可以减少一次页面请求，降低服务器负载；
好了，我们来开始来做这个登录的例子。首先我们先按照最原始的思路实现一个登录页面，功能要求就是当用户点击页顶部的登录链接时会跳转到登录页面。我先来完成基本的代码：

html代码：

``` html
<div id="top">
    <div class="wrapper">     
        <a href="login.html" id="signinlink">Sign In</a>
    </div>
</div>
```

基本的样式：

``` css
#top {
    width: 100%;
    border-botton: 1px solid #ccc;
    background: #eee;
    line-height: 24px;
    height: 24px;
    over-flow: hidden;
}
 
.wrapper {
    margin: 0 auto;
    width: 960px;
}
```

就这么简单，我们就完成了跳转到登录页面的功能。这个版本我相信在任何浏览器下都是能工作的，因为任何浏览器都是支持链接的，即使这个浏览器不支持javascript。下面我们要做的就是添加一些辅助代码用以表示登录弹出层的输入框等内容，html代码我修改为如下（其中id为signinpanel就是要弹出的层）：

``` html
<div id="top">
    <div class="wrapper">     
        <a href="login.html" id="signinlink">Sign In</a>
        <div id="signinpanel">
            <label for="username">username</label>
            <input id="username" name="username" />
            <label for="password">password</label>
            <input id="password" name="password" />
            <button>Sign In</button>
        </div>
    </div>
</div>
```

然后给登录链接添加事件，这里使用了jquery库：

``` javascript
<script>
$(function(){
    $('#signinlink').click(function(){
        $signinpanel = $('#signinpanel');
        if ($signinpanel.is(':hidden')) {
            $('#signinpanel').show();
        } else {
            $('#signinpanel').hide();
        }
 
        return false;
    });
});
</script>
```

虽然简单几行代码，却也是先了基本的弹出功能，return false；表示链接的原生时间被忽略掉了，点击链接只会处理我指定的时间。而当遇到不支持javascript的浏览器时，并不会影响功能的使用，单击登录链接仍然会跳转到登陆页面。

到此，demo完成。渐进增强的本质是强调内容本身，在内容丰富的基础上，对用户的体验进行优化，并且不会使功能受损。
title: "原生 JSON 工具类对象"
date: 2013-07-03
tags: Web Front
categories: 技术
---

今天才知道原生的JSON对象，惭愧惭愧， 这里对他的用法做一下记录。

我们都知道json是采用javascript对象的格式来存储数据的数据格式，这就不免和javascript对象之间经常转化，以前经常需要地方的库，现在不用了，原生的javascript自带了JSON对象，可以很方便的完成转化。JSON对象提供了2个方法：parse 和 stringify。

- JSON.parse：将json数据转为javascript对象
- JSON.stringify：将javascript对象序列化成json数据<!--more-->

用法：

- JSON.parse(json[, visitor])
- JSON.stringify(jso[, visitor[, space]])

稍微解释一下，visitor是在遍历过程中加入的自己的处理，space 是用来格式化输出的。不做过多解释，看一下代码并运行就明白了。

``` javascript
var json = '{"name":"adam", "age":20}';
// 直接转成javascript对象
var person = JSON.parse(json);
console.log(JSON.stringify(person));

// 转换的过程中修改某些值
var person2 = JSON.parse(json, function(k, v) {
	if (k == 'age') {
		return 30;
	}
	return v;
});
console.log(JSON.stringify(person2));
```

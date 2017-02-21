title: "Vi 常用命令汇总"
date: 2015-03-31 23:09:09
tags: Linux
categories: 技术
---

VI 是 unix/linux 系统下最常用的一个文本编辑工具，其命令也很多，本文是我以前从网上收藏的一边文章的，具体出处不记得了，贴在这里供自己查阅使用，同时向原作者表示感谢。

<!-- more -->

## 使用VI打开文件 

- <code>vi filename</code>：打开或新建文件，并将光标置于第一行首 
- <code>vi +n filename</code>：打开文件，并将光标置于第n行首 
- <code>vi + filename</code>：打开文件，并将光标置于最后一行首 
- <code>vi +/pattern filename</code>：打开文件，并将光标置于第一个与pattern匹配的串处 
- <code>vi -r filename</code>：在上次正用vi编辑时发生系统崩溃，恢复filename 
- <code>vi filename....filename</code>：打开多个文件，依次进行编辑 

## 移动光标类命令

- <code>h</code>：光标左移一个字符 
- <code>l</code>：光标右移一个字符 
- <code>space</code>：光标右移一个字符 
- <code>Backspace</code>：光标左移一个字符 
- <code>k或Ctrl+p</code>：光标上移一行 
- <code>j或Ctrl+n</code>：光标下移一行 
- <code>Enter</code>：光标下移一行 
- <code>w或W</code>：光标右移一个字至字首 
- <code>b或B</code>：光标左移一个字至字首 
- <code>e或E</code>：光标右移一个字至字尾 
- <code>)</code>：光标移至句尾 
- <code>(</code>：光标移至句首 
- <code>}</code>：光标移至段落开头 
- <code>{</code>：光标移至段落结尾 
- <code>nG</code>：光标移至第n行首 
- <code>n+</code>：光标下移n行 
- <code>n-</code>：光标上移n行 
- <code>n$</code>：光标移至第n行尾 
- <code>H</code>：光标移至屏幕顶行 
- <code>M</code>：光标移至屏幕中间行 
- <code>L</code>：光标移至屏幕最后行 
- <code>0</code>：（注意是数字零）光标移至当前行首 
- <code>$</code>：光标移至当前行尾 

## 屏幕翻滚类命令 

- <code>Ctrl+u</code>：向文件首翻半屏 
- <code>Ctrl+d</code>：向文件尾翻半屏 
- <code>Ctrl+f</code>：向文件尾翻一屏 
- <code>Ctrl＋b</code>：向文件首翻一屏 
- <code>nz</code>：将第n行滚至屏幕顶部，不指定n时将当前行滚至屏幕顶部。 

## 插入文本类命令

- <code>i</code>：在光标前 
- <code>I</code>：在当前行首 
- <code>a</code>：光标后 
- <code>A</code>：在当前行尾 
- <code>o</code>：在当前行之下新开一行 
- <code>O</code>：在当前行之上新开一行 
- <code>r</code>：替换当前字符 
- <code>R</code>：替换当前字符及其后的字符，直至按ESC键 
- <code>s</code>：从当前光标位置处开始，以输入的文本替代指定数目的字符 
- <code>S</code>：删除指定数目的行，并以所输入文本代替之 
- <code>ncw或nCW</code>：修改指定数目的字 
- <code>nCC</code>：修改指定数目的行 

## 删除命令 

- <code>ndw或ndW</code>：删除光标处开始及其后的n-1个字 
- <code>do</code>：删至行首 
- <code>d$</code>：删至行尾 
- <code>ndd</code>：删除当前行及其后n-1行 
- <code>x或X</code>：删除一个字符，x删除光标后的，而X删除光标前的 
- <code>Ctrl+u</code>：删除输入方式下所输入的文本 

## 搜索及替换命令 

- <code>/pattern</code>：从光标开始处向文件尾搜索pattern 
- <code>?pattern</code>：从光标开始处向文件首搜索pattern 
- <code>n</code>：在同一方向重复上一次搜索命令 
- <code>N</code>：在反方向上重复上一次搜索命令 
- <code>: s/p1/p2/g</code>：将当前行中所有p1均用p2替代 
- <code>: n1,n2s/p1/p2/g</code>：将第n1至n2行中所有p1均用p2替代 
- <code>: g/p1/s//p2/g</code>：将文件中所有p1均用p2替换 

## 撤销及重做

- `u`: 撤销
- `ctrl + r`: 重做

## 列编辑模式

通过 `ctrl + v` 启用列编辑模式，通过移动光标选取要编辑的行和列，如果想插入内容，则先 `shift + i` 进入插入模式，然后输入内容，输入完成后快速按两下 `esc` 既可多行插入；如果想删除内容，通过移动光标选取内容后，按下 `x` 后即可完成删除。

## 选项设置 

- <code>all</code>：列出所有选项设置情况 
- <code>term</code>：设置终端类型 
- <code>ignorance</code>：在搜索中忽略大小写 
- <code>list</code>：显示制表位(Ctrl+I)和行尾标志（$) 
- <code>number</code>：显示行号 
- <code>report</code>：显示由面向行的命令修改过的数目 
- <code>terse</code>：显示简短的警告信息 
- <code>warn</code>：在转到别的文件时若没保存当前文件则显示NO write信息 
- <code>nomagic</code>：允许在搜索模式中，使用前面不带"\"的特殊字符 
- <code>nowrapscan</code>：禁止vi在搜索到达文件两端时，又从另一端开始 
- <code>mesg</code>：允许vi显示其他用户用write写到自己终端上的信息 

## 最后行方式命令 

- <code>: n1,n2 co n3</code>：将n1行到n2行之间的内容拷贝到第n3行下 
- <code>: n1,n2 m n3</code>：将n1行到n2行之间的内容移至到第n3行下 
- <code>: n1,n2 d</code>：将n1行到n2行之间的内容删除 
- <code>: w</code>：保存当前文件 
- <code>: e filename</code>：打开文件filename进行编辑 
- <code>: x</code>：保存当前文件并退出 
- <code>: q</code>：退出vi 
- <code>: q!</code>：不保存文件并退出vi 
- <code>: !command</code>：执行shell命令command 
- <code>: n1,n2 w!command</code>：将文件中n1行至n2行的内容作为command的输入并执行之，若不指定n1，n2，则表示将整个文件内容作为command的输入 
- <code>: r!command</code>：将命令command的输出结果放到当前行 

## 寄存器操作 

- <code>"?nyy</code>：将当前行及其下n行的内容保存到寄存器？中，其中?为一个字母，n为一个数字 
- <code>"?nyw</code>：将当前行及其下n个字保存到寄存器？中，其中?为一个字母，n为一个数字 
- <code>"?nyl</code>：将当前行及其下n个字符保存到寄存器？中，其中?为一个字母，n为一个数字 
- <code>"?p</code>：取出寄存器？中的内容并将其放到光标位置处。这里？可以是一个字母，也可以是一个数字 
- <code>ndd</code>：将当前行及其下共n行文本删除，并将所删内容放到1号删除寄存器中。

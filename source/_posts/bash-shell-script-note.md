title: "bash shell 编程学习注记"
date: 2013-05-07
tags: Linux
---

常用shell有很多：bourne shell(/usr/bin/sh或者/bin/sh), Bourne Again Shell(/bin/bash), C Shell(/usr/bin/csh), K Shell(/usr/bin/ksh), Shell for Root(/sbin/sh)。每种shell都有自己的用法和语法。今天我主要学习的是 bash，因为bash比较广泛，是很多linux的默认shell。<!--more-->

## 首行

首行指定解释执行的主程序，#!/bin/sh, 其中#!是固定开头格式， /bin/sh 是执行的主程序，当然也可以选择其他的，例如 /bin/bash, /usr/bin/csh 等。

## 注释

# 标识此行为注释行，例如 # pring message here。

## 变量

格式：var_name=var_value，需要注意的是，=两边没有任何空格，例如 name=“zhjiun”。

双引号的字符串支持变量替换，例如 “Hello $name” 将被替换成 ”hello zhjiun“；单引号并不能完成变量替换。当变量与其他字符紧挨着时，应使用花括号将变量区别去来，例如 “Hello ${name}s”。

## 字符串

字符串要用双引号或者单引号包裹起来。不同的地方在于双引号支持变量替换，单引号则不支持。

``` bash 
#!/bin/bash

str="this is a string."
echo ${str:0:4}
echo ${str:5:2}

echo ${str#*is}
echo ${str##*is}

echo ${str%is*}
echo ${str%%is*}
```

${str:start_index:length}是截图字符串的子串。 后面几个包含#和%是从原串中干掉匹配的子串，返回留下的子串。#*is 标识从开头开始找，直到最近的is；##*is 表示从头开始找，直到最远is；%*is 表示从尾部开始找直到最近的is；%%*is 表示从尾部开始找直到最远的is。

字符串比较直接使用等号（=），比较时最好将变量使用双引号包裹起来，否则，当字符串中含有空格时，则会报错：参数过多。例如 [ "$str" = "my name is tim" ]。

## 逻辑判断

``` bash 
if [ condition ]
then
    echo '';
elif [ condition ]
then
    echo '';
else
    echo '';
fi

# 或者

if [ condition ]; then
    echo '';
elif [ condition ]; then
    echo '';
else
    echo '';
fi

# 或者 case, 这是一个判断文件后缀名的例子
case ${filename##*.} in
    gz) 
        action
        ;;
    tar)
        action
        ;;
    *)
        action
        ;;
esac
```

可以看出逻辑以 if 开始 fi 结束。 条件判断放在[]中，注意[]内部首位要各空一个空格。条件判断后面要紧跟一个分号，then才能放在同一行，如果没有分号，则then需要换行。

### 字符串比较运算符

- -z string 判断字符串长度是否为0
- -n string 判断字符串长度是否大于0
- str1 = str2 判断两个字符串内容是否相同
- str1 != str2 判断两个字符串是否不同

### 算术比较运算符

- -eq 等于
- -ne 不等于
- -lt 小于
- -gt 大于
- -le 小于等于
- -ge 大于等于

算术比较运算符只适用于比较数字类型的变量。

### 文件比较运算符

- -e filename 检查文件是否存在，存在则为真
- -d filename 检查是否为目录
- -f filename 检查是否为常规文件
- -L filename 检查是否为符号链接
- -r filename 检查是否可读
- -w filename 检查是否可写
- -x filename 检查是否可执行
- filename1 -nt filename2 检查filename1是否比filename2新
- filename1 -ot filename2 检查filename1是否比filename2旧

## 循环

``` bash for x in one two three four
do
    echo number $x
done

for myfile in ./*
do
    echo "$myfile"
done

while [ condition ]
do
    statements
done
```

现在基本上对bash shell变成有了入门的认识，想要提高，还得在实践中多写多练。

参考：

- http://www.cnblogs.com/suyang/archive/2008/05/18/1201990.html
- http://www.ibm.com/developerworks/cn/linux/shell/bash/bash-1/index.html
- http://www.ibm.com/developerworks/cn/linux/shell/bash/bash-2/index.html

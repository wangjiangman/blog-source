title: "File 对象的 separator 与  pathSeparator"
date: 2015-03-25
tags: Java
categories: 技术
---

File对象中提供了依赖于系统的分隔符，提供了4个静态方法：

- separatorChar char; 文件路径分割符: unix/linux 为 '/', windows 为 '\\'
- pathSeparatorChar char; 路径列表分隔符: unix/linux 为 ':', windows 为 ';'
- separator String; separatorChar的字符串类型
- pathSeparato String; pathSeparatorChar的字符串类型

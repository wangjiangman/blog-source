title: "记一次使用 Memory Analyzer 查找内存溢出"
date: 2015-11-12
tags: [Java]
---

Eclipse Memory Analyzer 是一个内存堆文件分析工具，基本上是傻瓜式的，可以很快帮我们生成内存报告，对我们监控内存或者出现内存溢出是分析问题非常有帮助。<!--more-->

## 产生对内存文件

Eclipse Memory Analyzer 是基于 heap dump 文件进行分析，所以分析之前，我们必须拿到对内存文件。一般有两种方法可以产生对内存文件：
- jmap命令
- 内存溢出时自动生成

### jmap 命令生成堆内存文件

以下是 jmap 命令的官方帮助 

```shell
Usage:
    jmap [option] <pid>
        (to connect to running process)
    jmap [option] <executable <core>
        (to connect to a core file)
    jmap [option] [server_id@]<remote server IP or hostname>
        (to connect to remote debug server)

where <option> is one of:
    <none>               to print same info as Solaris pmap
    -heap                to print java heap summary
    -histo[:live]        to print histogram of java object heap; if the "live"
                         suboption is specified, only count live objects
    -clstats             to print class loader statistics
    -finalizerinfo       to print information on objects awaiting finalization
    -dump:<dump-options> to dump java heap in hprof binary format
                         dump-options:
                           live         dump only live objects; if not specified,
                                        all objects in the heap are dumped.
                           format=b     binary format
                           file=<file>  dump heap to <file>
                         Example: jmap -dump:live,format=b,file=heap.bin <pid>
    -F                   force. Use with -dump:<dump-options> <pid> or -histo
                         to force a heap dump or histogram when <pid> does not
                         respond. The "live" suboption is not supported
                         in this mode.
    -h | -help           to print this help message
    -J<flag>             to pass <flag> directly to the runtime system
```

一般我们生成的命令如下：

```shell
jmap -dump:format=b,file=heap.hprof 30204
```

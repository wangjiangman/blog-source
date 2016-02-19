title: "垃圾优先垃圾回收器(G1 GC)使用笔记"
date: 2015-10-20
tags: [Java]
---

G1 GC 是一种新的垃圾回收策略，从 JDK7 开始，主要适用于服务器端的JVM，和大内存的应用，其目标是达到类似 CMS 的高吞吐量。G1 中依然有分代管理的思想，主要采用分块管理的思想，通过将内存分为不超过2048个块，每块大小在 1M-32M 之间， Eden、Survivor space 和 年老代 都是一系列不连续的逻辑区域。<!--more-->

这里不谈 G1 的具体的实现思路，只记录一下如何配置和使用。

## 启动 G1 GC

```shell
-XX:+UseG1GC
```

## 几个参数及默认值

- <code>-XX:MaxGCPauseMillis=200</code> 最大停顿时间, 这是一个目标值，JVM会尽量向此目标靠近，默认值为 200。
- <code>-XX:G1HeapRegionSize=n</code> G1 的区域块大小，1M-32M 之间，必须是2的幂，G1 会根据块大小规划出不大于 2048 个块。
- <code>-XX:G1NewSizePercent=5</code> 年轻代在堆中最小百分比，默认值是 5%
- <code>-XX:G1MaxNewSizePercent=60</code> 年轻代在堆中的最大百分比，默认值是 60%
- <code>-XX:ParallelGCThreads=n</code> 设置 STW 工作线程数的值。将 n 的值设置为逻辑处理器的数量。n 的值与逻辑处理器的数量相同，最多为 8。如果逻辑处理器不止八个，则将 n 的值设置为逻辑处理器数的 5/8 左右。这适用于大多数情况，除非是较大的 SPARC 系统，其中 n 的值可以是逻辑处理器数的 5/16 左右。
- <code>-XX:ConcGCThreads=n</code> 设置并行标记的线程数。将 n 设置为并行垃圾回收线程数 (ParallelGCThreads) 的 1/4 左右。
- <code>-XX:InitiatingHeapOccupancyPercent=45</code> 标记垃圾阀值， 默认45%
- <code>-XX:G1ReservePercent=10</code> 设置作为空闲空间的预留内存百分比，以降低目标空间溢出的风险。默认值是 10%。

## 建议

- 避免使用 -Xmn 选项或 -XX:NewRatio 等其他相关选项显式设置年轻代大小。固定年轻代的大小会覆盖暂停时间目标。
- 暂停时间目标不要太小，因为暂停时间与吞吐量是一个相互矛盾的指标。

## 例子

这是 tomcat 启动的一个简单配置

```shell
export CATALINA_OPTS="$CATALINA_OPTS -Xms10g"
export CATALINA_OPTS="$CATALINA_OPTS -Xmx10g"
export CATALINA_OPTS="$CATALINA_OPTS -Xss512k"

export CATALINA_OPTS="$CATALINA_OPTS -XX:+DisableExplicitGC"
export CATALINA_OPTS="$CATALINA_OPTS -XX:+HeapDumpOnOutOfMemoryError"
export CATALINA_OPTS="$CATALINA_OPTS -XX:+UnlockExperimentalVMOptions"

export CATALINA_OPTS="$CATALINA_OPTS -XX:+UseG1GC"
export CATALINA_OPTS="$CATALINA_OPTS -XX:MaxGCPauseMillis=200"
export CATALINA_OPTS="$CATALINA_OPTS -XX:G1NewSizePercent=40"
export CATALINA_OPTS="$CATALINA_OPTS -XX:G1MaxNewSizePercent=80"
```

> <code>G1NewSizePercent</code> 和 <code>G1MaxNewSizePercent</code> 为实验属性，所以请先 通过 <code>-XX:+UnlockExperimentalVMOptions</code> 打开实验选项。

Done.

参考：

- http://www.oracle.com/technetwork/cn/articles/java/g1gc-1984535-zhs.html

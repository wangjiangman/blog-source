Oracle 11g 中可采用自动化管理内存, 系统会自动调整内存区的大小.

几个参数:

memory_target, 采用自动内存管理是, 是一个动态的值, 为 Oracle 总共可以使用的共享内存大小. Oracle 可以动态的控制SGA和PGA. 最大值不会超过 memory_max_target 的值.

memory_max_target, 定义了 memory_target 可达的最大的内存值, 默认等于 memory_target.

修改内存设置语法

```
alter system set memory_max_target= 1000m scope=spfile;
alter system set  memory_target= 1000m scope=spfile;
```

scope 参数

- spfile 写入参数文件, 不立即生效, 下次启动生效.
- memory 仅在内存中更改, 立即生效, 下次启动失效.
- both 不但在内存中更改, 也写入参数文件, 立即生效, 下次启动依然有效.


参考

- http://blog.itpub.net/26686207/viewspace-754947/
- http://blog.csdn.net/perfect_db/article/details/8778256


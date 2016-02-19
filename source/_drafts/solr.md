
## 启动和关闭

### 启动

bin/solr start [option]

bin/solr start -help

bin/solr restart [option]

bin/solr restart -help

### 可选参数

- -a "<string>" 指定 JVM 参数
- -cloud / -c 已 SolrCloud 模式启动 solr, 同时也会启动内嵌的 zookeeper 实例.
- -d 定义 server 目录
- -e 启动带有例子的 solr. 可选:
  - cloud
  - techproducts
  - dih
  - schemaless
- -f 在 foreground 启动 solr. 一般通过 ctrl+c 推出
- -h 指定 hostname, 不指定时一般为 localhost
- -m 指定内存大小, 相当于 JVM 参数 -Xms -Xmx 指定相同的值
- -noprompt 抑制任何提示
- -p 指定启动端口，默认为 8983
- -s 指定系统属性 solr.solr.home. solr 会在这个目录下创建 core directory.
- -V Start Solr with verbose messages from the start script.\
- -z 指定 zookeeper 主机，只有在 SolrCloud Mode 才能使用， 和 -c 搭配使用。bin/solr start -c -z server1:2181,server2:2181

### 查状态

```
bin/solr status
```

### 停止

```
bin/solr stop [option]
bin/solr stop -help
bin/solr stop -p <port>
bin/solr stop -all
bin/solr stop -k <key>
```

### healthcheck

```
bin/solr healthcheck [option]
bin/solr healthcheck -help
bin/solr healthcheck -c <collection>
bin/solr healthcheck -z <zkHost>
```

## 目录结构

**Standalon Mode 标准单节点**

```
<solr-home-directory>/
    solr.xml
    core_name1/
        core.properties
        conf/
            solrconfig.xml
            schema.xml
        data/
    core_name2/
        core.properties
        conf/
            solrconfig.xml
            schema.xml
        data/
```

**SolrCloud Mode 云节点**

```
<solr-home-directory>/
    solr.xml
    core_name1/
        core.properties
        data/
    core_name2/
        core.properties
        data/
```

- solr.xml 对整个 solr server 进行了配置
- 对每一个 solr core 
    - core.properties 定义了当前core的基本特性，比如名称、当前core所属集合、schema的位置以及其他参数。
    - solrconfig.properties 控制更高层的行为。例如，你可以指定 data 目录的替换目录。
    - schema.xml 描述了需要索引的文档。你需要定义一个文档为一些字段的集合。你要定义字段类型以及字段本身。字段类型的定义很强大，包含了 solr 怎么处理和查询字段值的信息。
    - data 目录中包含了低层次的索引文件。


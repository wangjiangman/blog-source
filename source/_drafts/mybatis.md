
## log4j 配置 mybatis 输出查询时的结果集

``` xml
<!-- 记录mybatis的查询时sql语句和结果集， xxx.mapper是mybatis的Mapper类所在包 -->	
<logger name="xxx.mapper" additivity="true">
	<level value="trace" />
</logger>
```

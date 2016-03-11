title: "slf4j + log4j 2 配置"
date: 2015-06-30
tags: Java
categories: 技术
---


本文不提 log4j2 相比较 log4j 和 logback 有多么出色的性能提升，这里只记录 slf4j + log4j 2 的简单配置。

<!-- more -->

> maven 项目

## 增加 maven 依赖

``` xml
<properties>
	<slf4j.version>1.7.7</slf4j.version>
	<log4j.version>2.1</log4j.version>
</properties>

<dependencies>
	<dependency>
		<groupId>org.slf4j</groupId>
		<artifactId>slf4j-api</artifactId>
		<version>${slf4j.version}</version>
	</dependency>
	<dependency>
		<groupId>org.apache.logging.log4j</groupId>
		<artifactId>log4j-slf4j-impl</artifactId>
		<version>${log4j.version}</version>
	</dependency>
	<dependency>
		<groupId>org.apache.logging.log4j</groupId>
		<artifactId>log4j-api</artifactId>
		<version>${log4j.version}</version>
	</dependency>
	<dependency>
		<groupId>org.apache.logging.log4j</groupId>
		<artifactId>log4j-core</artifactId>
		<version>${log4j.version}</version>
	</dependency>
</dependencies>
```

## 配置 log4j2.xml

log4j 2 相比较 log4j 配置文件完全变了，需要重新配置。下面是一个demo配置，供参考。

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
	<appenders>
		<Console name="Console" target="SYSTEM_OUT">
			<PatternLayout pattern="%d %-5p %c{1} - %m%n" />
		</Console>
		<RollingFile name="DebugRollingFile" fileName="/www/logs/xxx/debug.log"
			filePattern="/www/logs/xxx/%d{yyyy-MM}/debug-%d{yyyy-MM-dd}-%i.log">
			<PatternLayout>
				<Pattern>%d %t %-5p - %m%n</Pattern>
			</PatternLayout>
			<Policies>
				<TimeBasedTriggeringPolicy />
				<SizeBasedTriggeringPolicy size="50 MB" />
			</Policies>
			<DefaultRolloverStrategy max="20"/>
		</RollingFile>
	</appenders>
	<Loggers>
		<Root level="debug">
			<AppenderRef ref="Console" />
			<AppenderRef ref="DebugRollingFile" level="debug" />
		</Root>
	</Loggers>
</Configuration>
```


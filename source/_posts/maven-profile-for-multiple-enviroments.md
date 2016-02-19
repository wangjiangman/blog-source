title: "Maven多环境配置管理几种方案"
date: 2014-06-25
tags: Java
---

开发环境、测试环境、生成环境…… 在开发中，我们需要面对各种各样的环境，并且，各个环境的参数和配置各不相同，比如数据库连接，服务器配置等。Maven作为一个项目管理和自动化构建工具，当然也给了我们解决方案，但是项目中的情况较多，这里列举几种常用的多环境的配置管理方案。
<!--more-->

## profile

Profile 是maven中的一个重要概念，他可以定义maven的一次运行环境档案。在 pom.xml 中我们可以定义多个profile，并可以在运行时通过参数将其中某个激活。一般 pom.xml 中的 profiles 节点定义如下：

``` xml
<profiles>
	<!-- 开发环境，默认激活 -->
	<profile>
		<id>dev</id>
		<properties>
			<running.env>dev</running.env>
		</properties>
		<activation>
			<activeByDefault>true</activeByDefault><!-- 这里表示默认环境 -->
		</activation>
	</profile>
	<!-- 测试环境 -->
	<profile>
		<id>test</id>
		<properties>
			<running.env>test</running.env>
		</properties>
	</profile>
	<!-- 生产环境 -->
	<profile>
		<id>prd</id>
		<properties>
			<running.env>prd</running.env>
		</properties>
	</profile>
</profiles>
```

以下的配置，都已此profile配置为基础。

## profile + 资源过滤

什么是资源过滤？资源过滤，就是在运行maven的过程中，对指定目录下的资源文件（xml，properties文件等）进行变量替换，用指定的变量值替换掉文件中的变量。

假设各个环境的数据库配置我们集中在jdbc.properties文件中配置， 以下是具体内容

``` text
# datasource properties
jdbc.driverClassName=oracle.jdbc.driver.OracleDriver
jdbc.db.url=${jdbc.db.url}
jdbc.db.username=${jdbc.db.username}
jdbc.db.password=${jdbc.db.password}
```

建立三个文件 config-dev.properties, config-test.properties, config-prd.properties 分别对应开发、测试、生产环境的配置。

config-dev.properties

``` text
jdbc.db.url=jdbc:oracle:thin:@192.168.1.10:1521:devdb
jdbc.db.username=devdb
jdbc.db.password=devdb
```

config-test.properties

``` text
jdbc.db.url=jdbc:oracle:thin:@10.3.1.23:1521:testdb
jdbc.db.username=testdb
jdbc.db.password=testdb
```

config-prd.properties

``` text
jdbc.db.url=jdbc:oracle:thin:@223.11.23.123:1521:onlinedb
jdbc.db.username=onlinedb
jdbc.db.password=onlinedb
```

启动资源过滤和指定配置来源

``` xml
<build>
	<resources>
		<resource>
			<directory>src/main/resources</directory>
			<filtering>true</filtering>
		</resource>
	</resources>
	<filters>
		<filter>src/main/resources/config-${running.env}.properties</filter>
	</filters>
</build>
```

resource节点指明目录，filtering节点为true，表示要过滤此目录。filters节点指明配置属性的来源，注意其中的路径中 ${running.env} 由 profile 中定义，就是在maven运行时能动态的根据当前的profile来获取 config 文件。

运行命令，mvn clean package，相当于 mvn clean package -P dev（通过-P来指定profile，这里的 dev 是 profile 的 id），因为 dev 是默认配置环境。 以下是指定不同的profile后，jdbc.properties的内容。

mvn clean package -P dev

``` text
# datasource properties
jdbc.driverClassName=oracle.jdbc.driver.OracleDriver
jdbc.db.url=jdbc:oracle:thin:@192.168.1.10:1521:devdb
jdbc.db.username=devdb
jdbc.db.password=devdb
```

mvn clean package -P test

``` text
# datasource properties
jdbc.driverClassName=oracle.jdbc.driver.OracleDriver
jdbc.db.url=jdbc:oracle:thin:@10.3.1.23:1521:testdb
jdbc.db.username=testdb
jdbc.db.password=testdb
```

mvn clean package -P prd

``` text
# datasource properties
jdbc.driverClassName=oracle.jdbc.driver.OracleDriver
jdbc.db.url=jdbc:oracle:thin:@223.11.23.123:1521:onlinedb
jdbc.db.username=onlinedb
jdbc.db.password=onlinedb
```

这种方式可以通过打包时指定profile来形成不同环境的程序包，但是也存在一个问题，由于必须需要运行 maven 命令来能生成包，而开发文件中是变量串，在开发的时候特别不方便，每次运行命令再部署调试给我们带来很大的麻烦。如何不通过变量替换，而只在打包的时候，动态的替换掉指定的变量？这样开发起来很方便了，打包也方便。 插件portable-config-maven-plugin提供了这种思路的实现，请继续。

## 插件 portable-config-maven-plugin

portable-config-maven-plugin 是由 Juven Xu 开发的一款配置替换插件，项目主页：https://github.com/juven/portable-config-maven-plugin，支持properties, xml 以及类 properties文件，具体使用方法从上面项目主页中有很明确的描述，这里不再赘述。

如果你的文件是xml，要求你使用 xpath语法来进行定位元素节点，当然这也没有什么难度。但是由于对xpath的不了解，在替换 log4j.xml 中的内容时，总是不能成功。这让我有点郁闷，在尝试几次没有解决后，转向了下面的方法。

另外还有一点，就是如果配置文件中有汉字的情况，替换后的配置文件中可能有乱码。

## profile + maven-war-plugin

原开发环境配置文件结构如下：

``` text
src/main/resource
	config
		config.properties
		jdbc.properties
	log4j.xml
```

新增 src/main/config 目录，用来存放各个环境的配置文件，删除掉 src/main/resource下的配置文件。结构如下：

``` text
src/main/config
	dev
		config
			config.properties
			jdbc.properties
		log4j.xml
	test
		config
			config.properties
			jdbc.properties
		log4j.xml
	prd
		config
			config.properties
			jdbc.properties
		log4j.xml
```

这样 dev, test, prd 目录下分别是配置文件的拷贝，但是内容中配置的值却匹配各自的环境。

profile的配置和最上面的保持一致，下面是 maven-war-plugin 的配置

``` xml
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-war-plugin</artifactId>
	<configuration>
		<webResources>
			<resource>
				<directory>src/main/config/${runing.env}</directory>
				<targetPath>WEB-INF/classes</targetPath>
			</resource>
		</webResources>
	</configuration>
</plugin>
```

其中也是通过resource节点中配置 ${running.env} 变量来动态的根据打包时的 -P 参数，拷贝不同目录下的配置文件到相应的位置。

打包的问题解决了，另外就是开发了，由于参数移动到 src/main/config/下，就需要进行一下设置，才能将此目录下的文件自动部署。Eclipse 下，右击项目，进入属性对话框，选择 deploy assembly 选项，将添加 /src/main/config/dev 的部署目录为 WEB-INF/classes 即可。

这种方式相比资源文件过滤，不需要变量绑定，方便了开发时调试，但是需要引入几套对应的配置文件，在每次增加/删除配置项时，其他环境的文件必须同步进行。

没有完美的方案，根据自己情况选择吧。
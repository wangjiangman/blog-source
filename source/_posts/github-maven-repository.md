title: "使用github作为maven仓库"
date: 2014-06-17
tags: [git, maven]
---

本文介绍的这种使用 github 作为 maven 仓库的思路主要为：

1. github的项目上创建mvn-repo分支，使用mvn-repo分支作为maven仓库
1. 配 置 pom.xml 使用 target/mvn-repo 作为本地部署的临时maven仓库
1. 部署时使用 github site-maven-plugin 将本地的maven仓库 push 到 github 的项目的分支 mvn-repo 上去

<!--more-->

以我的项目为例：https://github.com/buzheng/mybatis-pageable

首先通过配置pom.xml告诉maven将artifacts部署到一个target下的一个临时目录中，具体配置如下：

``` xml
<plugin>
	<artifactId>maven-deploy-plugin</artifactId>
	<version>2.8.1</version>
	<configuration>
		<altDeploymentRepository>internal.repo::default::file://${project.build.directory}/mvn-repo</altDeploymentRepository>
	</configuration>
</plugin>
```

配置完成后，运行命令 mvn clean deploy 后发现已经部署maven仓库到 target/mvn-repo 目录下。然后我们要做的就是将该目录上传到github中。

在 ~/.m2/settings.xml 中添加github的授权信息，以便 github 的 site-maven-plugin 能够push文件到github，配置信息如下：

``` xml
<server>
    <id>github</id>
    <username>你在github的帐号</username>
    <password>你在github的用户名</password>
</server>
```

添加如下配置到 pom.xml 以告诉 github site-maven-plugin 服务器信息

``` xml
<properties>
	<!-- github server corresponds to entry in ~/.m2/settings.xml -->
	<github.global.server>github</github.global.server>
</properties>
```

最后配置 github site-maven-plugin 插件

``` xml
<plugin>
	<groupId>com.github.github</groupId>
	<artifactId>site-maven-plugin</artifactId>
	<version>0.9</version>
	<configuration>
		<message>Maven artifacts for ${project.version}</message>  <!-- git commit message -->
		<noJekyll>true</noJekyll>                                  <!-- disable webpage processing -->
		<outputDirectory>${project.build.directory}/mvn-repo</outputDirectory> <!-- matches distribution management repository url above -->
		<branch>refs/heads/mvn-repo</branch>                       <!-- remote branch name -->
		<includes>
			<include>**/*</include>
		</includes>
		<repositoryName>mybatis-pageable</repositoryName>      <!-- github repo name -->
		<repositoryOwner>buzheng</repositoryOwner>    <!-- github username -->
	</configuration>
	<executions>
		<!-- run site-maven-plugin&#039;s &#039;site&#039; target as part of the build&#039;s normal 
			&#039;deploy&#039; phase -->
		<execution>
			<goals>
				<goal>site</goal>
			</goals>
			<phase>deploy</phase>
		</execution>
	</executions>
</plugin>
```

配置完成，运行命令 mvn clean deploy后，登录github，发现已经创建 mvn-repo 分支，并上传了 本地的 target/mvn-repo 目录。

``` bash
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building mybatis-pageable 0.1
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ mybatis-pageable ---
[INFO] Deleting E:\workspace\mybatis-pageable\target
[INFO]
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ mybatis-pageable ---
[WARNING] Using platform encoding (GBK actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] Copying 0 resource
[INFO]
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ mybatis-pageable ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 6 source files to E:/workspace/mybatis-pageable/target/classes
[INFO]
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ mybatis-pageable ---
[INFO] Not copying test resources
[INFO]
[INFO] --- maven-compiler-plugin:3.1:testCompile (default-testCompile) @ mybatis-pageable ---
[INFO] Not compiling test sources
[INFO]
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ mybatis-pageable ---
[INFO] Tests are skipped.
[INFO]
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ mybatis-pageable ---
[INFO] Building jar: E:/workspace/mybatis-pageable/target/mybatis-pageable-0.1.jar
[INFO]
[INFO] --- maven-install-plugin:2.4:install (default-install) @ mybatis-pageable ---
[INFO] Installing E:/workspace/mybatis-pageable/target/mybatis-pageable-0.1.jar to C:/Users/Adam.my-pc/.m2/repository/org/buzheng/mybatis-pageable/0.1/mybatis-pageable-0.1.jar
[INFO] Installing E:/workspace/mybatis-pageable/pom.xml to C:/Users/Adam.my-pc/.m2/repository/org/buzheng/mybatis-pageable/0.1/mybatis-pageable-0.1.pom
[INFO]
[INFO] --- maven-deploy-plugin:2.8.1:deploy (default-deploy) @ mybatis-pageable ---
[INFO] Using alternate deployment repository internal.repo::default::file://E:/workspace/mybatis-pageable/target/mvn-repo
Uploading: file://E:/workspace/mybatis-pageable/target/mvn-repo/org/buzheng/mybatis-pageable/0.1/mybatis-pageable-0.1.jar
Uploaded: file://E:/workspace/mybatis-pageable/target/mvn-repo/org/buzheng/mybatis-pageable/0.1/mybatis-pageable-0.1.jar (15 KB at 1020.2 KB/sec)
Uploading: file://E:/workspace/mybatis-pageable/target/mvn-repo/org/buzheng/mybatis-pageable/0.1/mybatis-pageable-0.1.pom
Uploaded: file://E:/workspace/mybatis-pageable/target/mvn-repo/org/buzheng/mybatis-pageable/0.1/mybatis-pageable-0.1.pom (4 KB at 907.5 KB/sec)
Downloading: file://E:/workspace/mybatis-pageable/target/mvn-repo/org/buzheng/mybatis-pageable/maven-metadata.xml
Uploading: file://E:/workspace/mybatis-pageable/target/mvn-repo/org/buzheng/mybatis-pageable/maven-metadata.xml
Uploaded: file://E:/workspace/mybatis-pageable/target/mvn-repo/org/buzheng/mybatis-pageable/maven-metadata.xml (303 B at 147.9 KB/sec)
[INFO]
[INFO] --- site-maven-plugin:0.9:site (default) @ mybatis-pageable ---
[INFO] Creating 9 blobs
[INFO] Creating tree with 10 blob entries
[INFO] Creating commit with SHA-1: ac8a117fa05c3a24f057a684519d5edade5d4e64
[INFO] Updating reference refs/heads/mvn-repo from cf1389181d9559b6926a26e852aa2b605b46da1e to ac8a117fa05c3a24f057a684519d5edade5d4e64
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 8.124s
[INFO] Finished at: Mon Jun 16 15:47:54 CST 2014
[INFO] Final Memory: 24M/178M
[INFO] ------------------------------------------------------------------------
```

到此为止，项目的已经部署到github上。如果其他项目依赖此项目，需要在pom.xml中添加如下配置后，再添加相应的依赖关系即可。

``` xml
<repositories>
	<repository>
		<id>mybatis-pageable-mvn-repo</id>
		<url>https://raw.github.com/buzheng/mybatis-pageable/mvn-repo/</url>
	</repository>
</repositories>
```

完成！

这种方法相当方便，只需要几个简单的配置，便能自动将jar包发布到github上。

参考：http://stackoverflow.com/questions/14013644/hosting-a-maven-repository-on-github

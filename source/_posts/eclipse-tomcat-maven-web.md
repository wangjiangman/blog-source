title: "在 Eclipse 中用 tomcat 调试 maven web 工程"
date: 2013-01-20
tags: Java
categories: 技术
---

在 Eclipse 中用 Jetty 调试 maven web 工程是很方便的，但是大家一般习惯tomcat，这里说一下如何在配置到tomcat中运行。<!--more-->

## 1、修改 project facets

右键工程，单击 properties， 在弹出的对话框左侧，点击 Project Facets，若工程还为配置 facets，则点击 Convert to faceted form...。

在右侧勾选 Dynamic Web Module 和 Java，version 根据自己的项目进行选择，在配置项里面输入正确的路径。最后 点击 OK。

![](/images/tomcat2.jpg)

## 2、增加部署配置

右键工程，单击 properties， 在弹出的对话框左侧，点击 Deployment Assembly，添加 Maven Dependencies 的部署位置 到 WEB-INF/lib.

![](/images/tomcat3.jpg)

![](/images/tomcat4.jpg)

## 3、运行工程

右键工程，选择 Run as ->Run on Server，在弹出的对话框中选择 tomcat 即可。
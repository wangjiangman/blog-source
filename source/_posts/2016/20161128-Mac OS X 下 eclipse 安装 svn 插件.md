title: "Mac OS X 下 eclipse 安装 svn 插件"
date: 2016-08-08
tags: [Mac, eclipse, svn]
categories: Mac使用记录
---

最近开发环境转到 mac 下，mac 属于 unix 系，使用起来跟 windows 差别较大，就连常用的 eclipse 下安装 svn 插件都遇到一些问题，主要报错为 JavaHL 找不到，本文记录一下解决方案。

<!--more-->

都知道 eclipse 下最常用的 svn 插件有 Subclipse(SVN 官方出品) 和 Subversive(eclipse官方出品)，我比较习惯使用 Subclipse，安装起来也轻车熟路，就是直接到 eclipse应用市场直接安装就可。

按下面步骤操作，弹出窗口中一路确认或者同意即可：

```
Help -> Eclipse Marketplace -> 搜索 svn -> Subclipse -> install 
```

这在 Windows 下直安装完成后就可以使用的，但是 mac 下不行，会报错： JavaHL not avaliable。根据[SVN官方](http://subclipse.tigris.org/wiki/JavaHL#head-5bf26515097c3231c1b04dfdb22c036bc511926b)的提示，安装 JavaHL即可，但由于我通过 HomeBrew 安装，所以先安装 HomeBrew。

1, 安装 HomeBrew

HomeBrew 是 mac 系统下一款软件管理工具，根据 [HomeBrew官方](https://brew.sh/)的提示通过如下命令即可安装

```shell
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

2, 安装 JavaHL

```shell
brew install --universal --Java subversion
```

3, 创建链接到 /Library/Java/Extensions

```shell
sudo ln -s /usr/local/lib/libsvnjavahl-1.dylib /Library/Java/Extensions/libsvnjavahl-1.dylib
```

如此操作后，重启 eclipse 后，即可 subeclipse 即可正常使用了。



title: "Virtualbox UEFI 配置问题"
date: 2016-03-23
tags: [Linux]
categories: 技术
---

最近在 Virtualbox 中安装 Ubuntu Server 时，由于在主板中选择了“启用EFI(只针对某些操作系统)”导致 Ubuntu 引导不起来，这里记录一下解决方案。<!--more-->

如何安装这里不再赘述，只是由于安装前配置的时候，选择了 “启用EFI(只针对某些操作系统)”，如下图所示，

![](/images/virtualbox-efi.png)

结果导致安装好 Ubuntu Server 重启后，引导不起来了，引导界面如下：

![](/images/virtualbox-efi-boot.png)

经过尝试，直接在Shell里运行 <code>FS0:\EFI\ubuntu\grubx64.efi</code> 可以成功引导，但是每次这么引导也很麻烦，可以用如下方式解决。

按顺序运行如下命令：

```shell
Shell> FS0:
FS0:\> cd EFI
FS0:\EFI> mkdir boot
FS0:\EFI> cp ubuntu\grubx64.efi boot\bootx64.efi 
```

然后重启就能自动引导了。

顺便查了下什么是 UEFI，这是来自 http://windows.microsoft.com/zh-cn/windows-8/what-uefi 的一段解释：

UEFI（统一可扩展固件接口）是适用于电脑的标准固件接口，旨在替换 BIOS（基本输入/输出系统）。 此标准由 UEFI 联盟中的 140 多个技术公司共同创建，其中包括 Microsoft。 UEFI 旨在提高软件互操作性和解决 BIOS 的限制。 UEFI 固件的一些优点包括：

- 通过帮助保护预启动或预引导过程，防御 bootkit 的攻击，从而提高安全性。
- 缩短了启动时间和从休眠状态恢复的时间。
- 支持容量超过 2.2 TB 的驱动器。
- 支持 64 位的现代固件设备驱动程序，系统在启动过程中可以使用它们来对超过 172 亿 GB 的内存进行寻址。
- UEFI 硬件可与 BIOS 结合使用。
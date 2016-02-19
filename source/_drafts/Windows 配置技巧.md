# Windows 配置技巧

## 配置 Windows Server 2003 远程桌面最大连接数

1，打开组策略编辑器窗口
方法：单击“开始”->“运行”，输入"gpedit.msc"，“确定”。

2，依次打开“计算机配置(computer configuration)→管理模板(administrative templates)→Windows 组件(windows components)→终端服务(terminal services)”

3，双击右侧的“限制连接数量(limit number of connections)”，再双击打开配置窗口（limit number of connections properties），选择enabled 将其TS maximum connections allowed 允许的最大连接数设置成需要的数量即可。
修改后别忘了重起服务器。

## Windows8.1 启用休眠
1. 按下 Win+R 组合键打开“运行”对话框并输入gpedit.msc，回车打开“组策略”。
1. 进入组策略界面，依次选择“计算机配置”、“管理模板”、“Windows组件”、"文件资源管理器"。
1. 在右边找到“在电源选项菜单中显示休眠”并双击打开，将其设为启用。

## Windows8.1 右键菜单使用习惯

右键菜单出现在左边，很不习惯。修改方法有2：

1. 控制面板 "平板电脑设置" 或者 "Tablet PC 设置", 如果找不到，在运行中输入 shell:::{80F3F1D5-FECA-45F3-BC32-752C152E456E}
2. 在弹出的对话框中，切换到 其他 选项卡，左右手使用习惯 设置成 惯用左手 即可。

## 管理员身份运行msi

以管理员身份运行 cmd，执行命令 msiexec /i x.msi

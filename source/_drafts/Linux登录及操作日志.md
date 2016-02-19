
## 登录日志

less /var/log/secure 
last
lastb
lastlog


/var/log/btmp 使用 lastb 打开

/var/log/btmp 记录所有失败登录信息。使用last命令可以查看btmp文件。例如，”last -f /var/log/btmp | more“。

/var/run/utmp 是一个二进制文件，记录当前登录系统的用户信息。可用who或w命令显示当中的内容

/var/log/lastlog 使用lastlog命令查看

```
echo > rm /var/log/secure
echo > /var/log/wtmp
echo > /var/log/btmp
echo > /var/run/utmp
echo > ~/.bash_history
history -c

```

```
rm -f /var/log/secure
rm -f /var/log/wtmp
rm -f /var/log/btmp
rm -f /var/run/utmp
rm -f /var/log/lastlog
rm -f .bash_history
echo > .bash_history
echo > .bash_logout
history -c
```

> 删除的文件会在重启系统后重新生成

```
find /var -type f -exec   rm   -v   {} \;
```

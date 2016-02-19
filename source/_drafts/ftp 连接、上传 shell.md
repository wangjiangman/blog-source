


``` shell
ftp -i -v -n 192.168.1.10 <<END
user oracle oracle
binary
mput file1.tar.gz file2.tar.gz
bye
END
```

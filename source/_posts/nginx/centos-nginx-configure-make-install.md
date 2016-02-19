title: "CentOS 编译安装 nginx 并配置缩略图插件"
date: 2015-05-28
tags: [Linux, Nginx]
---

yum安装nginx 非常方便，但是有些插件并不会默认安装，比如 http_image_filter_module， 因此我们需要编译安装 nginx，已达到我们的目的。

<!--more-->

## 安装依赖

``` shell
yum install -y pcre-devel libmxl2-devel libxslt-devel gd-devel
```

## 安装 nginx 

``` shell
wget http://nginx.org/download/nginx-1.9.1.tar.gz

tar -xzvf nginx-1.9.1.tar.gz
cd nginx-1.9.1

./configure\
  --user=nginx\
  --group=nginx\
  --with-http_ssl_module\
  --with-http_spdy_module\
  --with-http_realip_module\
  --with-http_addition_module\
  --with-http_xslt_module\
  --with-http_image_filter_module\
  --with-http_sub_module\
  --with-http_auth_request_module\
  --with-http_stub_status_module\
  --with-http_gzip_static_module 

make 
make install
```

安装完成后，可以使用如下命令来查看 nginx 安装的模块

``` shell
[root@linux001 ~]# /usr/local/nginx/sbin/nginx -V
nginx version: nginx/1.9.1
built by gcc 4.4.7 20120313 (Red Hat 4.4.7-11) (GCC) 
built with OpenSSL 1.0.1e-fips 11 Feb 2013
TLS SNI support enabled
configure arguments: --user=nginx --group=nginx --with-http_ssl_module --with-http_spdy_module --with-http_realip_module --with-http_addition_module --with-http_xslt_module --with-http_image_filter_module --with-http_sub_module --with-http_auth_request_module --with-http_stub_status_module --with-http_gzip_static_module --with-http_image_filter_module
```

## 增加启动脚本

新建文件 /etc/init.d/nginx ， 内容如下：

``` shell 
#!/bin/sh
#
# nginx - this script starts and stops the nginx daemon
#
# chkconfig:   - 85 15 
# description:  Nginx is an HTTP(S) server, HTTP(S) reverse \
#               proxy and IMAP/POP3 proxy server
# processname: nginx
# config:      /etc/nginx/nginx.conf
# config:    /usr/local/nginx/conf/nginx.conf
# pidfile:   /usr/local/nginx/logs/nginx.pid
 
# Source function library.
. /etc/rc.d/init.d/functions
 
# Source networking configuration.
. /etc/sysconfig/network
 
# Check that networking is up.
[ "$NETWORKING" = "no" ] && exit 0
 
nginx="/usr/local/nginx/sbin/nginx"
prog=$(basename $nginx)
 
NGINX_CONF_FILE="/usr/local/nginx/conf/nginx.conf"
 
[ -f /etc/sysconfig/nginx ] && . /etc/sysconfig/nginx
 
lockfile=/var/lock/subsys/nginx
 
make_dirs() {
   # make required directories
   user=`$nginx -V 2>&1 | grep "configure arguments:" | sed 's/[^*]*--user=\([^ ]*\).*/\1/g' -`
   if [ -z "`grep $user /etc/passwd`" ]; then
       useradd -M -s /bin/nologin $user
   fi
   options=`$nginx -V 2>&1 | grep 'configure arguments:'`
   for opt in $options; do
       if [ `echo $opt | grep '.*-temp-path'` ]; then
           value=`echo $opt | cut -d "=" -f 2`
           if [ ! -d "$value" ]; then
               # echo "creating" $value
               mkdir -p $value && chown -R $user $value
           fi
       fi
   done
}
 
start() {
    [ -x $nginx ] || exit 5
    [ -f $NGINX_CONF_FILE ] || exit 6
    make_dirs
    echo -n $"Starting $prog: "
    daemon $nginx -c $NGINX_CONF_FILE
    retval=$?
    echo
    [ $retval -eq 0 ] && touch $lockfile
    return $retval
}
 
stop() {
    echo -n $"Stopping $prog: "
    killproc $prog -QUIT
    retval=$?
    echo
    [ $retval -eq 0 ] && rm -f $lockfile
    return $retval
}
 
restart() {
    #configtest || return $?
    stop
    sleep 1
    start
}
 
reload() {
    #configtest || return $?
    echo -n $"Reloading $prog: "
    killproc $nginx -HUP
    RETVAL=$?
    echo
}
 
force_reload() {
    restart
}
 
configtest() {
  $nginx -t -c $NGINX_CONF_FILE
}
 
rh_status() {
    status $prog
}
 
rh_status_q() {
    rh_status >/dev/null 2>&1
}
 
case "$1" in
    start)
        rh_status_q && exit 0
        $1
        ;;
    stop)
        rh_status_q || exit 0
        $1
        ;;
    restart|configtest)
        $1
        ;;
    reload)
        rh_status_q || exit 7
        $1
        ;;
    force-reload)
        force_reload
        ;;
    status)
        rh_status
        ;;
    condrestart|try-restart)
        rh_status_q || exit 0
            ;;
    *)
        echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"
        exit 2
esac
```

添加好 /etc/init.d/nginx 后，需要增加执行权限，然后就可以用脚本来 启动、停止 nginx 了。

``` shell
chmod a+x /etc/init.d/nginx 

/etc/init.d/nginx start
/etc/init.d/nginx stop
/etc/init.d/nginx restart
/etc/init.d/nginx reload
```

或者以服务来 启动、停止 nginx。

``` shell
service nginx start
service nginx stop
service nginx restart
service nginx reload
```

## 设置自启动

``` shell
chkconfig --add nginx
chkconfig --level 345 on
```

Done!




title: "CentOS 7 编译安装 Nginx 1.11.10"
date: 2017-02-15
tags: [CentOS, Nginx]
categories: 技术
---

以前写过一篇 [CentOS 编译安装 nginx 并配置缩略图插件](//buzheng.org/nginx/centos-nginx-configure-make-install.html)，当时的环境是 CentOS 6.x 和 Nginx 1.9.x 。 本次安装的环境为 CentOS 7.2 和  Nginx 1.11.10, 有些配置以前自启动的方式发生了变化， 这里再记录一下。
<!--more-->

## 安装编译安装依赖的类库和包

```
yum install gcc gcc-c++ autoconf automake  zlib zlib-devel openssl openssl-devel pcre-devel
```

## 下载和解压 Nginx 1.11.10

wget http://nginx.org/download/nginx-1.11.10.tar.gz
tar -xzvf nginx-1.11.10.tar.gz
cd nginx-1.11.10

## 配置 Nginx 安装选项

这里编译了插件 http_image_filter_module, 来实现图片的处理，采用默认的安装路径进行安装。

```bash
./configure\
  --with-http_ssl_module\
  --with-http_v2_module\
  --with-http_realip_module\
  --with-http_addition_module\
  --with-http_xslt_module\
  --with-http_image_filter_module\
  --with-http_sub_module\
  --with-http_auth_request_module\
  --with-http_stub_status_module\
  --with-http_gzip_static_module 
```

运行结果如下

```bash
[root@xx nginx-1.11.10]# ./configure\
>   --with-http_ssl_module\
>   --with-http_v2_module\
>   --with-http_realip_module\
>   --with-http_addition_module\
>   --with-http_xslt_module\
>   --with-http_image_filter_module\
>   --with-http_sub_module\
>   --with-http_auth_request_module\
>   --with-http_stub_status_module\
>   --with-http_gzip_static_module
checking for OS
 + Linux 3.10.0-327.22.2.el7.x86_64 x86_64
checking for C compiler ... found
 + using GNU C compiler
 + gcc version: 4.8.5 20150623 (Red Hat 4.8.5-11) (GCC) 
checking for gcc -pipe switch ... found
checking for -Wl,-E switch ... found
checking for gcc builtin atomic operations ... found
checking for C99 variadic macros ... found
checking for gcc variadic macros ... found
checking for gcc builtin 64 bit byteswap ... found
checking for unistd.h ... found
checking for inttypes.h ... found
checking for limits.h ... found
checking for sys/filio.h ... not found
checking for sys/param.h ... found
checking for sys/mount.h ... found
checking for sys/statvfs.h ... found
checking for crypt.h ... found
checking for Linux specific features
checking for epoll ... found
checking for EPOLLRDHUP ... found
checking for EPOLLEXCLUSIVE ... not found
checking for O_PATH ... found
checking for sendfile() ... found
checking for sendfile64() ... found
checking for sys/prctl.h ... found
checking for prctl(PR_SET_DUMPABLE) ... found
checking for sched_setaffinity() ... found
checking for crypt_r() ... found
checking for sys/vfs.h ... found
checking for poll() ... found
checking for /dev/poll ... not found
checking for kqueue ... not found
checking for crypt() ... not found
checking for crypt() in libcrypt ... found
checking for F_READAHEAD ... not found
checking for posix_fadvise() ... found
checking for O_DIRECT ... found
checking for F_NOCACHE ... not found
checking for directio() ... not found
checking for statfs() ... found
checking for statvfs() ... found
checking for dlopen() ... not found
checking for dlopen() in libdl ... found
checking for sched_yield() ... found
checking for SO_SETFIB ... not found
checking for SO_REUSEPORT ... found
checking for SO_ACCEPTFILTER ... not found
checking for SO_BINDANY ... not found
checking for IP_BIND_ADDRESS_NO_PORT ... not found
checking for IP_TRANSPARENT ... found
checking for IP_BINDANY ... not found
checking for IP_RECVDSTADDR ... not found
checking for IP_PKTINFO ... found
checking for IPV6_RECVPKTINFO ... found
checking for TCP_DEFER_ACCEPT ... found
checking for TCP_KEEPIDLE ... found
checking for TCP_FASTOPEN ... found
checking for TCP_INFO ... found
checking for accept4() ... found
checking for eventfd() ... found
checking for int size ... 4 bytes
checking for long size ... 8 bytes
checking for long long size ... 8 bytes
checking for void * size ... 8 bytes
checking for uint32_t ... found
checking for uint64_t ... found
checking for sig_atomic_t ... found
checking for sig_atomic_t size ... 4 bytes
checking for socklen_t ... found
checking for in_addr_t ... found
checking for in_port_t ... found
checking for rlim_t ... found
checking for uintptr_t ... uintptr_t found
checking for system byte ordering ... little endian
checking for size_t size ... 8 bytes
checking for off_t size ... 8 bytes
checking for time_t size ... 8 bytes
checking for AF_INET6 ... found
checking for setproctitle() ... not found
checking for pread() ... found
checking for pwrite() ... found
checking for pwritev() ... found
checking for sys_nerr ... found
checking for localtime_r() ... found
checking for posix_memalign() ... found
checking for memalign() ... found
checking for mmap(MAP_ANON|MAP_SHARED) ... found
checking for mmap("/dev/zero", MAP_SHARED) ... found
checking for System V shared memory ... found
checking for POSIX semaphores ... not found
checking for POSIX semaphores in libpthread ... found
checking for struct msghdr.msg_control ... found
checking for ioctl(FIONBIO) ... found
checking for struct tm.tm_gmtoff ... found
checking for struct dirent.d_namlen ... not found
checking for struct dirent.d_type ... found
checking for sysconf(_SC_NPROCESSORS_ONLN) ... found
checking for openat(), fstatat() ... found
checking for getaddrinfo() ... found
checking for PCRE library ... found
checking for PCRE JIT support ... found
checking for OpenSSL library ... found
checking for zlib library ... found
checking for libxslt ... found
checking for libexslt ... found
checking for GD library ... found
checking for GD WebP support ... not found
creating objs/Makefile

Configuration summary
  + using system PCRE library
  + using system OpenSSL library
  + using system zlib library

  nginx path prefix: "/usr/local/nginx"
  nginx binary file: "/usr/local/nginx/sbin/nginx"
  nginx modules path: "/usr/local/nginx/modules"
  nginx configuration prefix: "/usr/local/nginx/conf"
  nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
  nginx pid file: "/usr/local/nginx/logs/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"
  nginx http access log file: "/usr/local/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"
```

最后的几行是配置的结果， 注意这里的 pid file 那一行，后面配置服务的时候，需要用的到。


## 安装 Nginx

使用如下命令来安装 nginx 。

```
make && make install
```

## 配置 systemd Nginx 服务和设置 Nginx 自启动

centos 7 中采用 systemd 来管理系统，我们来为 nginx 创建服务文件，来实现通过 systemd 来管理 nginx 。

创建 systemd 服务文件： `/lib/systemd/system/nginx.service`，内容如下：

```
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

**这里的 PIDFile 的配置要和前面的 配置保持一致。**

设置自启动 

```
systemctl enable nginx
```

通过 systemd 管理 nginx

```
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl reload nginx
```

打完收工。
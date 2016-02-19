title: "Nginx 插件实现缩略图并本地缓存"
date: 2015-06-02
tags: Nginx
---

Nginx 带了图片插件 http_image_filter_module，可以很方便的实现缩略图功能，只是默认的情况下并不会安装，需要自己编译安装才能行。编译安装的步骤参考 [CentOS 编译安装 nginx 并配置缩略图插件](/nginx/centos-nginx-configure-make-install.html) .

<!--more-->

安装前需要配置如下，必须带有 with-http_image_filter_module 的行：

``` shell
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
```

先说一下需求：

*要达到的要求*

- 原图 http://192.168.1.20/g/15/1500001001/g-1.jpg
- 小图 http://192.168.1.20/g/15/1500001001/g-1-S.jpg
- 中图 http://192.168.1.20/g/15/1500001001/g-1-M.jpg
- 大图 http://192.168.1.20/g/15/1500001001/g-1-L.jpg

*具体的存放路径*

- 原图 /home/www/images/g/15/1500001001/g-1.jpg
- 小图 /home/www/images/_cache/g/15/1500001001/g-1-S.jpg
- 中图 /home/www/images/_cache/g/15/1500001001/g-1-M.jpg
- 大图 /home/www/images/_cache/g/15/1500001001/g-1-L.jpg

配置

``` shell
[root@linux001 conf.d]# vi images-server.conf
server {
    listen       80;
    server_name  localhost;
    root   /home/www/images;

    #charset koi8-r;

    #access_log  logs/image-server.access.log  main;

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }

    location ~ ^/g/(\d+)/(\d+)/g-(\d+)-([LMS])\.jpg$ {

        root /home/www/images/_cache;

        set $size $4;

        set $w "-";
        set $h "-";

        if ($size = "S") {
            set $w "120";
        }
        if ($size = "M") {
            set $w "600";
        }
        if ($size = "L") {
            set $w "1001";
        }

        set $img_filename g/$1/$2/g-$3.jpg;
        set $file "/home/www/images/$img_filename";

        if (!-f $file) {
            break;
        }

        if (!-f $request_filename) {
            proxy_pass http://127.0.0.1/_cache/$img_filename?width=$w&height=$h;
            break;
        }
        
        proxy_store /home/www/images/_cache/g/$1/$2/g-$3-$4.jpg;
        #proxy_store on;
        proxy_store_access   user:rw  group:rw  all:r;
        proxy_temp_path      /tmp/images;
        proxy_set_header     Host $host;
    }

    location ~ ^/_cache/(.*)$ {
        #root /home/www/images;
        rewrite ^/_cache/(.*)$ /$1 break;

        image_filter_buffer 20M;
        image_filter resize $arg_width $arg_height;
        image_filter_jpeg_quality 75;
    }

}
```

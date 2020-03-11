
---

title: nginx安装和使用

tags: 
	[nginx,限流,安装]

---

# nginx的编译安装

[下载nginx](!http://nginx.org/en/download.html) 选择版本，我这里使用v1.15.4

解压

`tar xvf nginx-1.15.4.tar.gv`

最好重命名该源码
`mv nginx-1.15.4 nginx-1.15.4-source`

进入目录cd nginx-1.15.4-source

使用下面编译安装命令

```
./configure  --prefix=/home/nginx/nginx-1.15.4  --sbin-path=/home/nginx/nginx-1.15.4/sbin/nginx --conf-path=/home/nginx/nginx-1.15.4/conf/nginx.conf --error-log-path=/home/nginx/nginx-1.15.4/logs/error.log  --http-log-path=/home/nginx/nginx-1.15.4/logs/access.log  --pid-path=/home/nginx/nginx-1.15.4/pid/nginx_6379.pid --lock-path=/home/nginx/nginx-1.15.4/lock/nginx.lock  --user=nginx --group=nginx --with-http_ssl_module --with-http_stub_status_module --with-http_gzip_static_module --http-client-body-temp-path=/home/nginx/nginx-1.15.4/tmp/client/ --http-proxy-temp-path=/home/nginx/nginx-1.15.4/tmp/proxy/ --http-fastcgi-temp-path=/home/nginx/nginx-1.15.4/tmp/fcgi/ --http-uwsgi-temp-path=/home/nginx/nginx-1.15.4/tmp/uwsgi --http-scgi-temp-path=/home/nginx/nginx-1.15.4/tmp/scgi --with-pcre
```
<!--more-->
安装

`make && make install`

启动有时候会出现目录问题,切换到/home/nginx/nginx-1.15.4下
`mkdir -p tmp/client tmp/proxy tmp/fcgi tmp/scgi lock tmp/uwsgi`





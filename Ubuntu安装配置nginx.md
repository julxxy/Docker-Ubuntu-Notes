



# 1 Ubuntu安装配置nginx

## 1.1 nginx下载

官方网站下载 nginx：http://nginx.org/

本资料使用 nginx-1.19.7.tar.gz（2020年10月29日10:31:41） 版本；windows版本略微简单，有兴趣可自行尝试。

## 1.2 安装nginx依赖环境

```shell
#安装编译环境
sudo apt-get install -y build-essential

#安装依赖软件
sudo apt -y update
sudo apt -y upgrade
sudo apt-get install -y openssl libssl-dev libpcre3 libpcre3-dev zlib1g-dev
sudo apt-get install -y libxml2 libxml2-dev libxslt-dev
sudo apt-get install -y libgd-dev
sudo apt-get install -y libxml2 libxml2-dev
sudo apt-get install -y libxslt-devel
sudo apt-get install -y pcre pcre-devel openssl openssl-devel gd-devel zlib-devel
sudo apt -y upgrade

```



## 1.3 nginx安装

- ## 1.3.1 把 nginx 的源码包nginx-1.19.7.tar.gz上传到 linux 系统或者使用wget下载

我们这里采用的是SecureCRT的rz命令上传至ubuntu操作系统里面，创建nginx文件夹：

```shell
#去官网找个最新的稳定版，https://nginx.org/en/download.html

NGINX_VERSION="1.20.2"

clear && mkdir -pv /usr/local/nginx  && cd /usr/local/nginx

#使用wget下载nginx
wget http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz

filename="nginx-${NGINX_VERSION}.tar.gz"
tar -zxvf $filename
ll
rm -rfv $filename

#在终端输入 rz 选择上传，sz是下载命令
apt install -y lrzsz
rz
```



正常可以看到以下文件夹：

![image-20201029104425890](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201029104425890.png)

- 进入nginx-1.19.7目录   使用 configure 命令创建一 Makefile文件:

```shell
NGINX_VERSION="1.20.2"

cd /usr/local/nginx/nginx-${NGINX_VERSION} && clear && ll
./configure \
--prefix=/usr/local/nginx \
--pid-path=/var/run/nginx/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--http-client-body-temp-path=/var/temp/nginx/client \
--http-proxy-temp-path=/var/temp/nginx/proxy \
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
--http-scgi-temp-path=/var/temp/nginx/scgi \
--with-http_ssl_module \
--with-http_mp4_module \
--with-http_sub_module \
--with-http_stub_status_module \
--with-http_gzip_static_module \
--with-http_realip_module \
--with-http_auth_request_module \
--with-http_v2_module \
--with-http_dav_module \
--with-http_slice_module \
--with-http_addition_module \
--with-http_gunzip_module \
--with-http_image_filter_module=dynamic \
--with-http_sub_module \
--with-http_xslt_module=dynamic \
--with-debug \
--with-compat \
--with-pcre-jit \
--with-threads \
--with-stream=dynamic \
--with-stream_ssl_module \
--with-mail=dynamic \
--with-mail_ssl_module
```



- 知识点小贴士-1

Makefile是一种配置文件， Makefile 一个工程中的源文件不计数，其按类型、功能、模块分别放在若干个目录中，makefile定义了一系列的规则来指定，哪些文件需要先编译，哪些文件需要后编译，哪些文件需要重新编译，甚至于进行更复杂的功能操作，因为 makefile就像一个Shell脚本一样，其中也可以执行操作系统的命令。

------

- 知识点小贴士-2

configure参数：

```makefile
./configure \
--prefix=/usr \ #指向安装目录
--sbin-path=/usr/sbin/nginx \ #指向（执行）程序文件（nginx）
--conf-path=/etc/nginx/nginx.conf \ #指向配置文件
--error-log-path=/var/log/nginx/error.log \  #指向log
--http-log-path=/var/log/nginx/access.log \ #指向http-log
--pid-path=/var/run/nginx/nginx.pid \ #指向pid
--lock-path=/var/lock/nginx.lock \ #安装文件锁定，防止安装文件被别人利用，或自己误操作
--user=nginx \
--group=nginx \
--with-http_ssl_module \ #启用ngx_http_ssl_module支持（使支持https请求，需已安装openssl）
--with-http_flv_module \ #启用ngx_http_flv_module支持（提供寻求内存使用基于时间的偏移量文件）
--with-http_stub_status_module \ #启用ngx_http_stub_status_module支持（获取nginx自上次启动以来的工作状态）
--with-http_gzip_static_module \ #启用ngx_http_gzip_static_module支持（在线实时压缩输出数据流）
--http-client-body-temp-path=/var/tmp/nginx/client/ \ #设定http客户端请求临时文件路径
--http-proxy-temp-path=/var/tmp/nginx/proxy/ \ #设定http代理临时文件路径
--http-fastcgi-temp-path=/var/tmp/nginx/fcgi/ \ #设定http fastcgi临时文件路径
--http-uwsgi-temp-path=/var/tmp/nginx/uwsgi \ #设定http uwsgi临时文件路径
--http-scgi-temp-path=/var/tmp/nginx/scgi \ #设定http scgi临时文件路径
--with-pcre \ #启用pcre库
--add-module=/usr/local/fastdfs/fastdfs-nginx-module-1.22/src \#添加fastdfs模块，解压后fastdfs-nginx-module所在的位置,如果你安装了"fastdfs"的话下面这行的注释打开
--with-debug \
--with-compat \
--with-pcre-jit \
--with-http_stub_status_module \
--with-http_realip_module \
--with-http_auth_request_module \
--with-http_v2_module \
--with-http_dav_module \
--with-http_slice_module \
--with-threads \
--with-http_addition_module \
--with-http_gunzip_module \
--with-http_gzip_static_module \
--with-http_image_filter_module=dynamic \
--with-http_sub_module \
--with-http_xslt_module=dynamic \
--with-stream=dynamic \
--with-stream_ssl_module \
--with-mail=dynamic \
--with-mail_ssl_module 
```

## 1.4 编译安装

编译&安装：

```shell
make && make install
```



## 1.5 nginx启动与访问

**注意**：

启动nginx 之前，上边将临时文件目录指定为/var/temp/nginx/client， 需要在 /var 下创建此目录；

```shell
clear && mkdir -pv /var/temp/nginx/client
```

进入到Nginx目录下的sbin目录：

```shell
cd /usr/local/nginx/sbin && ./nginx
```

或者：

```
clear && /usr/local/nginx/sbin/nginx
```

启动后查看进程：

```shell
ps aux | grep nginx
```

成功应该可以看到:

![image-20201029113052966](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201029113052966.png)

浏览器输入ip可以看到nginx的欢迎页面：
http://192.168.1.142/

**换成自己的ip**

![image-20201029113340630](\Ubuntu安装配置nginx.assets\image-20201029113340630.png)

## 1.6 将nginx的主程序加入系统变量

**将nginx的主程序加入到系统环境变量**:
和将java添加到ubuntu加入系统环境变量方法一样,配置环境变量:
使用vim编辑/etc/profile:

```shell
vim /etc/profile

#在后面添加

#
# iginx env
#
export NGINX_HOME=/usr/local/nginx
export PATH=$PATH:$NGINX_HOME/sbin
```

单击ESC退出vim编辑器，输入`:wq` 保存。

使配置文件立即生效，执行：

```shell
source /etc/profile
```

以后可在终端上直接使用`nginx`命令来重载`nginx`配置。

检验配置是否成功：

```shell
cd  / && nginx -v
```

成功如下：

![image-20201029114844975](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201029114844975.png)

## 1.7 nginx常用命令

**查看nginx默认安装模块和自定义安装模块**：

```shell
#查看nginx默认安装模块和自定义安装模块
clear && nginx -V
```

![image-20201029115140790](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201029115140790.png)

我只之前已经配置好了nginx的全局系统变量， 所以直接在终端使用`nginx`指令。

```shell
#优雅停止nginx，有连接时会等连接请求完成再杀死worker进程  
nginx -s quit
#优雅重启，并重新载入配置文件nginx.conf
nginx -s reload
#重新打开日志文件，一般用于切割日志
nginx -s reopen
#查看版本  
nginx -v
#检查nginx的配置文件
nginx -t
#查看帮助信息
nginx -h
#详细版本信息，包括编译参数 
nginx -V
#指定配置文件，filename为你的nginx的具体配置文件位置，如：/usr/local/nginx/conf/my_nginx.conf
nginx  -c filename
```

## 1.8 nginx配置https证书

- 创建证书目录：

```shell
mkdir -p /usr/local/nginx/cert
```

将`.pem`和`.key`两个证书文件上传至**证书目录**后:

- 修改配置nginx.conf文件:

```nginx
    # HTTPS server
    server {
        listen       443 ssl;
        server_name  www.bbc.com bbc.com;
        
        #以下两个文件换成自己测ca证书
        ssl_certificate      /usr/local/nginx/cert/3267994_www.bbc.com.pem;
        ssl_certificate_key  /usr/local/nginx/cert/3267994_www.bbc.com.key;

        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;

        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;
    
        location / {
            root   html;
            index  index.html index.htm;
        }
    
        # 自定义逻辑(可以换成自己location代码块)
        location /travel {
            proxy_pass http://127.0.0.1:8080/travel;
            proxy_redirect off;
            proxy_ssl_verify off;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header REMOTE-HOST $remote_addr;
            proxy_set_header Host $http_host;
            proxy_set_header cookie $http_cookie;
            proxy_set_header Proxy-Connection "";
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
	    }
    }
```



## 1.9 设置nginx快速启动脚本

**开机启动nginx**

```shell
rm -rfv ~/nginx_run.sh
sudo tee ~/nginx_run.sh <<-'EOF'
#!/bin/bash
BASE_DIR="/usr/local/nginx"
CONFIG="${BASE_DIR}/conf/nginx.conf"
sudo kill $(sudo lsof -t -i:80)
mkdir -pv /var/run/nginx && rm -rfv /var/run/nginx/nginx.pid && touch /var/run/nginx/nginx.pid
${BASE_DIR}/sbin/nginx -t
${BASE_DIR}/sbin/nginx -c ${CONFIG}
echo "nginx启动成功！"
EOF
# 给文件增加权限
chmod -vR 777 ~/nginx_run.sh
```


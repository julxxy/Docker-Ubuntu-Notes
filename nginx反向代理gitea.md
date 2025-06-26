# 1 nginx反向代理gitea代码仓库示例

## 1.1 修改配置nginx.conf文件

```nginx
# clear && /usr/local/nginx/sbin/nginx -s reload
# clear && /usr/local/nginx/sbin/nginx -t

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    #解决"nginx could not build the server_names_hash"的方法
    server_names_hash_bucket_size 64;

    server {
        listen       80;
        server_name  localhost 172.26.224.8;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        #http --> https
        #if ($scheme = http) {
        #    return 301 https://$server_name$request_uri;
        #}
        #if ($ssl_protocol = "") {
        #    return 301 https://$server_name$request_uri;
        #}

        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Server $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header REMOTE-HOST $remote_addr;
        proxy_set_header X-Forwarded-For $remote_addr; #设置请求源地址
        proxy_set_header X-Forwarded-Proto $scheme; #设置Http协议
        add_header X-Cache $upstream_cache_status;

        proxy_headers_hash_max_size 512;
        proxy_headers_hash_bucket_size 128;

        #Set Nginx Cache
        add_header Cache-Control no-cache;

        client_max_body_size 50m;

        #rabbitmq后台界面
        location /rabbitmq/ {
            proxy_pass http://localhost:15672;            
            rewrite "^/rabbitmq/(.*)$" /$1 break;
	        proxy_connect_timeout 600;
            proxy_read_timeout 600;
        }

        #默认代理到git仓库
        location / {
            proxy_pass http://localhost:3000;
            proxy_connect_timeout 600;
            proxy_read_timeout 600;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```

## 1.2  重载nginx.conf文件

```shell
clear && /usr/local/nginx/sbin/nginx  -s reload
```


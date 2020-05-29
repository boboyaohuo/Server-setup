# linux安装nginx步骤

## 1.安装nginx依赖包

    //一键安装上面四个依赖
    yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel

## 2.下载并解压安装包

    //创建一个文件夹
    cd /usr/local
    mkdir nginx
    cd nginx
    //下载tar包
    wget http://nginx.org/download/nginx-1.16.1.tar.gz
    tar -zxvf nginx-1.16.1.tar.gz

## 3.安装nginx

    //进入nginx目录
    cd /usr/local/nginx/nginx-1.16.1
    //执行命令
    ./configure
    //执行make命令
    make
    //执行make install命令
    make install
## 4.配置nginx.conf

    # 打开配置文件
    vi /usr/local/nginx/conf/nginx.conf
    端口号改成8089，避免与apache80端口重复
    
## 5.启动nginx

    /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
    查看nginx进程是否启动：
    ps -ef | grep nginx

## 6.若想使用外部主机连接上虚拟机访问，需要关闭虚拟机的防火墙：

centOS6及以前版本使用命令： systemctl stop iptables.service

centOS7关闭防火墙命令： systemctl stop firewalld.service

随后访问该ip即可看到nginx界面

## 7.nginx启动 关闭 重启命令
    启动：/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
    关闭：/usr/local/nginx/sbin/nginx -s stop
    重启: /usr/local/nginx/sbin/nginx -s reload
    注：在sbin目录执行命令的话，./nginx - stop 就可以
    
## 8.nginx.conf配置信息（ssl，favicon，重定向，反向代理）
    
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

        gzip  on;
        gzip_min_length 10k;
        gzip_buffers 4 16k;
        gzip_comp_level 3;
        gzip_types text/plain application/javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;


        server {
            listen 80;
            server_name www.wujianbo.com wujianbo.com;
            rewrite ^/(.*) https://$host/$1 permanent;
        }

        server {
            listen 80;
            server_name github.wujianbo.com;
            rewrite ^/(.*) https://github.com/boboyaohuo permanent;
        }

        server {
            listen 80;
            server_name weibo.wujianbo.com;
            rewrite ^/(.*) https://weibo.com/6023938741 permanent;
        }

        server {
            listen 80;
            server_name jenkins.wujianbo.com;	

            location / {
                proxy_pass http://localhost:8080/;
                add_header Access-Control-Allow-Origin *;
            }

            location = /favicon.ico {
                root  /var/www/html;
                log_not_found off;
                access_log off;
            }
        }

        server {
            listen 80; 
            server_name 150.109.118.154;
            rewrite ^/(.*) https://www.wujianbo.com permanent;
        }

        # HTTPS server
        #
        server {
            listen       443 ssl;
            server_name  www.wujianbo.com wujianbo.com;

            ssl_certificate      1_www.wujianbo.com_bundle.crt;
            ssl_certificate_key  2_www.wujianbo.com.key;
            ssl_session_timeout  5m;
            ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
            ssl_ciphers  ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
            ssl_prefer_server_ciphers  on;

            location / {
                root   /var/www/html;
                index  index.html index.htm;
                try_files $uri $uri/ /index.html;
            }

            location = /favicon.ico {
                root  /var/www/html;
                log_not_found off;
                access_log off;
            }
        }
    }


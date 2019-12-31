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
    tar -xvf nginx-1.16.1.tar.g

## 3.安装nginx

    //进入nginx目录
    cd /usr/local/nginx
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

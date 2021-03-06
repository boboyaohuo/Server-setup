# Linux下安装mysql

## 注意1

>本文档讲解安装版本为mysql-5.7.24，对于5.7.24之后的版本，不适用此说明文档，主要原因在于之后版本的mysql配置文件的目录位置和结构有所改变，使用此说明可能会出现找不到配置文件或者配置后不生效的情况。

## 注意2

>安装过程中务必保证文件路径的前后统一，否则可能会导致不可预期的结果，推荐直接使用文中的命令进行操作。

## 一 安装前准备

1、检查是否已经安装过mysql，执行命令

    [root@localhost /]# rpm -qa | grep mysqz

如果已经安装了mysql，执行删除命令

    [root@localhost /]# rpm -e --nodeps mysql-libs-5.1.73-5.el6_6.x86_64
      
再次执行查询命令，查看是否删除

2、查询所有Mysql对应的文件夹

    [root@localhost /]# whereis mysql
    mysql: /usr/bin/mysql /usr/include/mysql
    [root@localhost lib]# find / -name mysql
    /data/mysql
    /data/mysql/mysql

删除相关目录或文件

    [root@localhost /]#  rm -rf /usr/bin/mysql /usr/include/mysql /data/mysql /data/mysql/mysql 
    
验证是否删除完毕

    [root@localhost /]# whereis mysql
    mysql:
    [root@localhost /]# find / -name mysql
    [root@localhost /]# 
    
3、检查mysql用户组和用户是否存在，如果没有，则创建

    [root@localhost /]# cat /etc/group | grep mysql
    [root@localhost /]# cat /etc/passwd |grep mysql
    [root@localhost /]# groupadd mysql
    [root@localhost /]# useradd -r -g mysql mysql
    
4、从官网下载是用于Linux的Mysql安装包 或者 浏览器下载传到服务器

    [root@localhost /]#  wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
    
# 二 安装Mysql

1、在执行wget命令的目录下或你的上传目录下找到Mysql安装包：mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
执行解压命令：

    [root@localhost /]#  tar xzvf mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
    [root@localhost /]# ls
    mysql-5.7.24-linux-glibc2.12-x86_64
    mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
    
解压完成后，可以看到当前目录下多了一个解压文件，移动该文件到/usr/local/下，并将文件夹名称修改为mysql。

>如果/usr/local/下已经存在mysql，请将已存在mysql文件修改为其他名称，否则后续步骤可能无法正确进行。

执行命令如下：

    [root@localhost /]# mv mysql-5.7.24-linux-glibc2.12-x86_64 /usr/local/mysql
    
2、在/usr/local/mysql目录下创建data目录

    [root@localhost /]# mkdir /usr/local/mysql/data
    
3、更改mysql目录下所有的目录及文件夹所属的用户组和用户，以及权限

    [root@localhost /]# chown -R mysql:mysql /usr/local/mysql
    [root@localhost /]# chmod -R 755 /usr/local/mysql
    
4、编译安装并初始化mysql,务必记住初始化输出日志末尾的密码（数据库管理员临时密码）

    [root@localhost /]# cd /usr/local/mysql/bin
    [root@localhost bin]# ./mysqld --initialize --user=mysql --datadir=/usr/local/mysql/data --basedir=/usr/local/mysql
    
5、运行初始化命令成功后，输出日志如下：

>记录日志最末尾位置root@localhost:后的字符串，此字符串为mysql管理员临时登录密码。

6.编辑配置文件my.cnf，添加配置如下

    [root@localhost bin]#  vi /etc/my.cnf

    [mysqld]
    datadir=/usr/local/mysql/data
    port = 3306
    sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
    symbolic-links=0
    max_connections=400
    innodb_file_per_table=1
    #表名大小写不明感，敏感为
    lower_case_table_names=1
    
7、启动mysql服务器

    [root@localhost /]# /usr/local/mysql/support-files/mysql.server start
    
如果出现 starting mysql ok 表示启动成功

如果出现 Starting MySQL... ERROR! The server quit without updating PID file 表示进程存在

    #查询服务
    ps -ef|grep mysql
    ps -ef|grep mysqld

    #结束进程
    kill -9 PID

    #启动服务
     /usr/local/mysql/support-files/mysql.server start

8、添加软连接，并重启mysql服务

    [root@localhost /]#  ln -s /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql 
    [root@localhost /]#  ln -s /usr/local/mysql/bin/mysql /usr/bin/mysql
    [root@localhost /]#  service mysql restart

9、登录mysql，修改密码(密码为步骤5生成的临时密码)

    [root@localhost /]#  mysql -u root -p
    Enter password:
    mysql>set password for root@localhost = password('yourpass');

10、开放远程连接

    mysql>use mysql;
    msyql>update user set user.Host='%' where user.User='root';
    mysql>flush privileges;

11、设置开机自动启动

    1、将服务文件拷贝到init.d下，并重命名为mysql
    [root@localhost /]# cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
    2、赋予可执行权限
    [root@localhost /]# chmod +x /etc/init.d/mysqld
    3、添加服务
    [root@localhost /]# chkconfig --add mysqld
    4、显示服务列表
    [root@localhost /]# chkconfig --list

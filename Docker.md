# Linux 下的 Docker 安装与使用

    docker为应用容器引擎，可提供沙箱式环境，可搭配nginx反向代理一起使用

## 一、安装与配置

1. 安装依赖包

        sudo yum install -y yum-utils device-mapper-persistent-data lvm2
        
2. 设置阿里云镜像源

        sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
        
3. 安装 Docker-CE

+ 重建 Yum 缓存
+ 安装 Docker-CE ，请执行一下命令进行安装：

        sudo yum install docker-ce
        
4. 启动 Docker-CE

        sudo systemctl enable docker
        sudo systemctl start docker

[命令行大全](https://www.runoob.com/docker/docker-command-manual.html)
![avatar](http://img3.imgtn.bdimg.com/it/u=4161548211,506321560&fm=15&gp=0.jpg)

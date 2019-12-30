# 记一次jenkins搭建

## 环境
1. linux centOS 7
2. JDK
3. tomcat
4. jenkins

## 软件
1. finalshell

## 搭建过程
### 搭建linux centOS 7 环境 服务器默认配置，自己虚拟机搭建过程（略）

1. 命令: ip addr 查看Linux虚拟机的IP地址,并通过finalshell进行连接**

2. 命令:arch 核查Linux系统的位数,根据位数下载对应的JDK以及Tomact**

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/01.png)

3. jenkins下载命令 # wget http://mirrors.jenkins.io/war/latest/jenkins.war**

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/02.png)
> 备注:可以将所有的软件下载到Windows,通过xftp上传到服务器,也可以在Linux服务器中直接下载

4. 解压jdk和tomcat 解压文件到当前目录命令 #tar -zxvf 文件名**

### 配置JDK环境变量

在JDK文件目录下输入命令,进行编辑 # vi /etc/profile 通过上下键定位到本页面最下面, 输入i进入编辑状态.此时会对应的出现INSET标志,表示处于可编辑状态

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/03.png)

    export JAVA_HOME=**jdk绝对路径（修改项）**
    export JRE_HOME=${JAVA_HOME}/jre
    export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH
    export JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin
    export PATH=$PATH:${JAVA_PATH}

- 插入上述内容，根据真实地址更改修改项。

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/04.png)

- 编辑完成后,输入ESC退出,此时INSERT小时,表示退出了编辑状态,此时输入  :wq 表示保存退出.回车

- 编辑的文件生效 命令：**# source /etc/profile**

- 检查是否配置成功 命令 **# javac**

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/05.png)

### 配置Tomcat的环境变量
与JDK配置相同,首先定位到Tomact文件目录下,通过vi /ect/profile 命令进入编辑状态进行编辑,保存退出,让文件生效

>export TOMCAT_HOME=tomact绝对路径(修改项)

- 将上述内容加到末尾，根据真实地址更改修改项，退出同上

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/06.png)

- 然后进入bin文件夹 **# cd bin**

- 启动tomcat **# sh startup.sh**

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/07.png)

### Jenkins在Linux下部署
将jenkins.war包放到tomcat/webapps/路径下， 启动Tomcat服务器,自动jar包会自动解压,生成Jenkins文件夹

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/08.png)

>Tomact服务器默认端口号为8080,CentOs 7对8080端口没有开放.所以此时你访问Jenkins是无法访问的

- 解决方案:firewalled进行8080端口的开发配置

- 开放8080端口
firewall-cmd --zone=public --add-port=8080/tcp --permanent

- 重启防火墙
firewall-cmd --reload

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/09.png)

#### 启动Tomcat,此时我们的本地浏览器就可以访问Jenkins,访问地址为,Linux虚拟机的IP:8080/jenkins/

## jenkins设置过程

### 设置过程问题
1. 首次访问设置插件处，离线模式

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/10.png)

- 打开 ip:8080/pluginManager/advanced
- https://updates.jenkins.io/update-center.json 修改https为http

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/11.png)

- 重启Jenkins ip:8080/jenkins/resart

2. 汉化问题
- 安装插件“Locale plugin”和“Localization: Chinese (Simplified)”

- 在global configure里将语言设定为zh_US，Jenkins切换为英文。

- 调用restart重启Jenkins：http://域名/restart。

- 再次语言设定为zh_CN，刷新即可。

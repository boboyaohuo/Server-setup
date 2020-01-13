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

1. 命令: ip addr 查看Linux虚拟机的IP地址,并通过finalshell进行连接

2. 命令:arch 核查Linux系统的位数,根据位数下载对应的JDK以及Tomact

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/01.png)

3. jenkins下载命令 # wget http://mirrors.jenkins.io/war/latest/jenkins.war

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/02.png)
> 备注:可以将所有的软件下载到Windows,通过xftp上传到服务器,也可以在Linux服务器中直接下载

4. 解压jdk和tomcat 解压文件到当前目录命令 #tar -zxvf 文件名




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


### jenkins根域名设置
以tomcat应用服务器为例，我们将一个jenkins的项目部署到tomcat的webapps下面，启动tomcat，默认输入地址：http://localhost:8080/jenkins 访问项目，但是我们如何通过输入http://自己想要的域名 来直接访问项目呢，在此以jenkins.wujianbo.com为示例域名。

1. 使用nginx反向代理，将80端口 jenkins.wujianbo.com反向代理到 http://localhost:8080/ 此时，你如果再去访问项目，只需要输入http://jenkins.wujianbo.com/jenkins 就可以访问了。

2. 修改tomcat的conf文件夹下的server.xml文件：将engine的defaultHost改为jenkins.wujianbo.com
        
        <Engine name="Catalina" defaultHost="jenkins.lilian.com">

3. 将Host的name改为自己想要的域名
        
        <Host name="jenkins.lilian.com"  appBase="webapps"

4. 在<Host></Host>内部加入<Context/>标签，如下：
        
        <Host name="jenkins.lilian.com"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
        <Context docBase="jenkins" path="" reloadable="true" />   加入词句
        
enkins即为项目名称，path为默认

5、重启tomcat，输入http://jenkins.wujianbo.com 试试吧！
> 注：通过反向代理之后，会造成jenkins接口的403错误，是因为 jenkins 默认安全设置里面开启了 防止款站点请求伪造。
> 解决方法：取消勾选 **Jenkins > 全局安全配置 > CSRF Protection** ，就可以了


### 设置过程问题

### 1. 首次访问设置插件处，离线模式

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/10.png)

- 打开 ip:8080/pluginManager/advanced
- https://updates.jenkins.io/update-center.json 修改https为http

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/11.png)

- 重启Jenkins ip:8080/jenkins/resart



### 2. 汉化问题
- 安装插件“Locale plugin”和“Localization: Chinese (Simplified)”

- 在global configure里将语言设定为zh_US，Jenkins切换为英文。

- 调用restart重启Jenkins：http://域名/restart。

- 再次语言设定为zh_CN，刷新即可。



### 3. 权限问题
- 安装插件“Role-based Authorization Strategy”

- 全局管理里授权策略更改成role-base

- 系统管理里会出现Manager and Assign role管理项

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/12.png)

## jenkins 初始化使用

1. 在浏览器输入http://服务器IP:8080 打开jenkins，首次打开需要获取管理员的密码

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/13.png)

2. 安装默认插件：输入密码，提交完成之后会跳转到插件安装页面，选择第一个然后进行安装

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/14.png)

3. 接下来默认插件安装完成之后，创建一个管理员账户，完成配置后，就可以登录 Jenkins 了

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/15.png)

4. 安装 NodeJs插件，用于vue项目打包构建。

打开系统管理 => 管理插件 搜索 NodeJs然后勾选安装
打开系统管理 => 全局工具配置 拉到底部 配置 node 版本

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/16.png)
![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/17.png)

## jenkins 配合github 实现vue等前端项目的自动构建与发布

1. 新建任务：点击新建任务 => 输入任务名称，选择构建一个自由风格的软件项目然后确定

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/18.png)
![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/19.png)

2. 配置git，进入任务配置，选择源码管理 ，因为github上的项目是开源的，所以无需填写账号密码

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/20.png)
![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/21.png)
![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/22.png)

3. 设置构建环境，选择 Provide Node & npm bin/ folder to PATH 然后选择之前安装插件时候配置的node版本

![avatar](https://raw.githubusercontent.com/boboyaohuo/staticFile/master/image/23.png)

4. 置项目自动化打包，选择 增加构建步骤 => Excute shell 这个是运行相关的sh命令（这一步建议耗时操作分离步骤）

        cd /var/lib/jenkins/workspace/hxkj #进入Jenkins工作空间下hxkj项目目录
        node -v #检测node版本（此条命令非必要）
        npm -v #检测npm版本（此条命令非必要）
        npm config set registry https://registry.npm.taobao.org #把npm源设置为淘宝源（这个你懂的）
        npm config get registry #检测npm是否切换成功（此条命令非必要）
        npm install #安装项目中的依赖
        npm run build #打包
        cd dist
        rm -rf hxkj.tar.gz #删除上次打包生成的压缩文件（一般建议备份，不要直接删除，这边测试就无所谓啦）
        tar -zcvf hxkj.tar.gz * #把生成的项目打包成压缩包，方便移动到项目部署目录
        cd /usr/share/nginx/hxkj #进入web项目根目录
        mv /var/lib/jenkins/workspace/hxkj/dist/hxkj.tar.gz ./  #移动刚刚打包好的项目到web项目根目录
        tar -zxvf hxkj.tar.gz -C dist/  #解压项目到dist目录
        rm -rf hxkj.tar.gz    #删除压缩包
        
5. 保存后点击立即构建查看任务是否能够成功构建，控制台输出 菜单可以查看构建日志。

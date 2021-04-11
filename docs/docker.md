# Docker

## 一、安装

**1.较旧的Docker版本称为docker或docker-engine。如果已安装这些程序，请卸载它们以及相关的依赖项。**

`sudo yum remove docker \`                  

​								 `docker-client \`                 

​								 `docker-client-latest \`                 

​								 `docker-common \`                 

​								 `docker-latest \`                 

​								 `docker-latest-logrotate \`                 

​								 `docker-logrotate \`                 

​								 `docker-engine`

**2.大多数用户会 设置Docker的存储库并从中进行安装，以简化安装和升级任务。这是推荐的方法。一下基于此种方法安装docker：**

**设置存储库**

**安装yum-utils软件包（提供yum-config-manager 实用程序）并设置稳定的存储库。**

`sudo yum install -y yum-utils`

`sudo yum-config-manager \    --add-repo \    https://download.docker.com/linux/centos/docker-ce.repo`

**3.安装DOCKER引擎**

`sudo yum install docker-ce docker-ce-cli containerd.io`

**4.启动Docker**

`sudo systemctl start docker`

**5.通过运行hello-world 映像来验证是否正确安装了Docker Engine 。**

`sudo docker run hello-world`

**6.关闭docker**

`sudo systemctl stop docker`

**7.阿里镜像加速器**

`sudo mkdir -p /etc/docker sudo tee /etc/docker/daemon.json <<-'EOF' {  "registry-mirrors": ["https://jzuzu44h.mirror.aliyuncs.com"] } EOF` 

**8.重启docker**

`sudo systemctl daemon-reload` 

`sudo systemctl restart docker`

## 二.设置用户权限

无权限报错：

`Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/json?all=1: dial unix /var/run/docker.sock: connect: permission denied`

**方案一：使用sudo获取管理员权限，运行docker命令**

​	`sudo docker ps -a`

注：

<u>在使用sudo命令前时，用户确保在sudoers文件中已添加xxx ALL=(ALL) ALL (这里的xxx是你的用户名)，否则</u>

<u>会报xxx is not in the sudoers file.This incident will be reported的错误。</u>

解决方法：

1.切换到root用户：su root

2.添加sudoers文件的写权限，命令是:	`chmod u+w /etc/sudoers`

3.编辑sudoers文件

![](D:\JAVA_NOTE\docs\images\docker_dusoers.png)

4.撤销sudoers文件写权限,命令:	`chmod u-w /etc/sudoers`

**方案二：添加docker group组，将用户添加进去**

添加进去后，每次命令操作无需sudo

`sudo groupadd docker` #添加docker用户组
`sudo gpasswd -a $USER docker` #将当前用户添加至docker用户组
`newgrp docker` #更新docker用户组
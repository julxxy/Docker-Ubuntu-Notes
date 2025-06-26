**本文基于 Ubuntu 20.04.1 LTS (GNU/Linux 5.4.0-52-generic x86_64) 实践**

# 1 Docker安装与卸载

## 1.1 更新你的ubuntu系统

### 1.1.1 切换Ubuntu的默认镜像源

[阿里云官方镜像站](https://developer.aliyun.com/mirror/?spm=a2c6h.13651104.0.d1002.240794ddxBJvco)

![image-20201120233454764](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201120233454764.png)

直接点对应的镜像源即可：

![image-20201120233600531](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201120233600531.png)

以ubuntu 20.04(focal) 为例：

> `EOF`代码块中的镜像源链接是你需要从阿里云官网复制来替换的

```bash
#!/usr/bin/env bash

#备份Ubuntu原来的镜像源文件
rm -rfv /etc/apt/sources.list.bak
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

#删除文件
rm -rfv /etc/apt/sources.list

#创建升级upgrade.sh文件
sudo tee /etc/apt/sources.list <<-'EOF'
#ubuntu 20.04(focal)阿里云镜像源
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
EOF

#重载daemon守护进程
sudo systemctl daemon-reload


```

### 1.1.2 更新软件列表和软件包

> 知识贴士:
>
> ```shell
> sudo apt-get -y install   #参数: -y，自动填写Y,  安装时不需要用户输入Y进行确认
> ```
>
>

```shell
#!/usr/bin/env bash

rm -rfv ~/upgrade.sh
#创建升级upgrade.sh文件
sudo tee ~/upgrade.sh <<-'EOF'
sudo apt-get -y update
sudo apt-get -y upgrade
sudo apt list --upgradable
sudo apt-get dist-upgrade -y
sudo apt-get -y clean
sudo apt-get -y autoclean
sudo apt-get -y autoremove
EOF
#赋予执行权限
sudo chmod -v 0777 ~/upgrade.sh
#执行升级
~/upgrade.sh
#重载daemon守护进程
sudo systemctl daemon-reload

```

## 1.2  开始安装

### 1.2.1 Install Docker Engine on Ubuntu by Official

[官方安装教程](https://docs.docker.com/engine/install/ubuntu/)

### 1.2.2 Uninstall old versions

Older versions of Docker were called docker, docker.io, or docker-engine. If these are installed, uninstall them:

```shell
sudo apt-get remove docker docker-engine docker.io containerd runc
```

### 1.2.3 安装方式

You can install Docker Engine in different ways, depending on your needs:

- Most users [set up Docker’s repositories](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)
  and install from them, for ease of installation and upgrade tasks. This is the recommended approach.
- Some users download the DEB package
  and [install it manually](https://docs.docker.com/engine/install/ubuntu/#install-from-a-package) and manage upgrades
  completely manually. This is useful in situations such as installing Docker on air-gapped systems with no access to
  the internet.
- In testing and development environments, some users choose to use
  automated [convenience scripts](https://docs.docker.com/engine/install/ubuntu/#install-using-the-convenience-script)
  to install Docker.

#### 1.2.3.1 使用[repository](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)安装

1. Update the `apt` package index and install packages to allow `apt` to use a repository over HTTPS:

```shell
sudo apt-get -y update

sudo apt-get -y install \
  apt-transport-https \
  ca-certificates \
  curl \
  gnupg-agent \
  software-properties-common
```

2. Add Docker’s official GPG key

```sh
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

3. Verify that you now have the key with the fingerprint `9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88`, by
   searching for the last 8 characters of the fingerprint.

```shell
sudo apt-key fingerprint 0EBFCD88
```

![image-20201104231558195](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201104231558195.png)

4. Use the following command to set up the **stable** repository. To add the **nightly** or **test** repository, add the
   word `nightly` or `test` (or both) after the word `stable` in the commands below. [Learn about **nightly** and **test
   ** channels](https://docs.docker.com/engine/install/).

   ***下面三个选择对应自己机器的版本***

   > **提示：**
   >
   > - 使用指令查看系统配置相关信息：
   >
   > ```shell
   > sudo uname -a
   > ```
   >
   >
   >
   > ![image-20201104232800258](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201104232800258.png)
   >
   > - 查看系统其他信息：
   >
   > ```shell
   > sudo uname --s #显示内核名字
   > sudo uname --r #显示内核版本
   > sudo uname --n #显示网络主机名
   > sudo uname --p #显示cpu 
   > ```
   >
   > ![image-20201104233330267](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201104233330267.png)


- **x86_64 / amd64**

官方源:

```shell
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

阿里云源:

```shell
sudo add-apt-repository \
   "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

- **arm64**

官方源:

```shell
sudo add-apt-repository \
   "deb [arch=arm64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

阿里云源:

```shell
sudo add-apt-repository \
   "deb [arch=arm64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

- **armhf**

官方源:

```shell
sudo add-apt-repository \
   "deb [arch=armhf] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

阿里云源:

```shell
sudo add-apt-repository \
   "deb [arch=armhf] http://mirrors.aliyun.com/docker-ce/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

#### 1.2.3.2 安装Docker Engine

Update the `apt` package index, and install the *latest version* of Docker Engine and containerd, or go to the next step
to install a specific version:

```shell
sudo apt-get -y update
sudo apt-get -y install docker-ce docker-ce-cli containerd.io
```

### 1.2.4 配置Docker镜像加速

## 1. 安装／升级Docker客户端

推荐安装1.10.0以上版本的Docker客户端，参考文档 [docker-ce](https://yq.aliyun.com/articles/110806)

## 2. 配置aliyun镜像加速器

针对Docker客户端版本大于 1.10.0 的用户

您可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://7ge0bal0.mirror.aliyuncs.com","https://docker.mirrors.ustc.edu.cn","http://hub-mirror.c.163.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker.service
sudo systemctl enable docker.service
```

## 1.3 卸载Docker

1. Uninstall the Docker Engine, CLI, and Containerd packages:

```shell
sudo apt-get purge docker-ce docker-ce-cli containerd.io
```

2. Images, containers, volumes, or customized configuration files on your host are not automatically removed. To delete
   all images, containers, and volumes:

```shell
sudo rm -rfv /var/lib/docker
```

You must delete any edited configuration files manually.

## 1.4 系统开机启动Docker

```shell
sudo systemctl enable docker
```

## 1.5 禁用Docker开机启动

```shell
sudo systemctl disable docker
```

## 1.6 重启Docker服务

```shell
sudo systemctl daemon-reload
sudo systemctl restart docker.service
```

```bash
rm -rfv ~/docker_restart.sh
sudo tee ~/docker_restart.sh <<-'EOF'
sudo systemctl daemon-reload
sudo systemctl restart docker.service
EOF
sudo chmod -v 0777 ~/docker_restart.sh
~/docker_restart.sh
sudo systemctl daemon-reload
```

## 1.7 通过netstat输出审查，确认dockerd的端口是否配置上

```shell
sudo netstat -lntp | grep dockerd
```

![image-20201104230342131](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201104230342131-1604502232761.png)

# 2 Docker常用插件安装

## 2.1 Docker安装Ubuntu镜像

### 2.1.1 搜索镜像

[dockerhub链接](https://hub.docker.com/_/ubuntu?tab=tags&page=1&ordering=last_updated), 不指定tag, 默认拉取最新版;

### 2.1.2 安装ubuntu

```shell
#!/usr/bin/env bash

# 镜像名称
image_name="ubuntu"
# 容器名称
container_name="ubuntu"
# 镜像版本
version="latest"
previous_version="20.04"

# 拉取镜像
docker rmi -f ${previous_version}:${version} && docker pull ${image_name}:${version}

# 查看镜像
clear && docker images

#运行容器
docker run -itd --name ubuntu ubuntu:${version}

#进入容器内部，查看安装版本
docker exec -it ubuntu bash

# 执行uname -a可以显示当前内核的版本信息
# root@187ca2427516:/# uname -a
# Linux 187ca2427516 5.8.0-44-generic #50~20.04.1-Ubuntu SMP Wed Feb 10 21:07:30 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux

# 退出容器
exit

# 删除容器
docker rm -f ubuntu
```

## 2.2 安装Oracle JDK镜像

先把Oracle JDK下载上传至服务器的指定位置，一般是：/usr/local/jdk

```shell
mkdir -p /usr/local/jdk
cd /usr/local/jdk
# 上传jdk到此目录
```

### 2.2.1 安装jdk1.8

- 编写Dockerfile（Dockerfile-jdk1.8）

```shell
#!/usr/bin/env bash

# 定义变量
WORKSPACE="/usr/local/jdk"
# jdk的小版本号
RELEASE_VERSION="281"
# jdk压缩包的名称
TAR_NAME="jdk-8u${RELEASE_VERSION}-linux-x64.tar.gz"
# 解压后jdk文件夹的名称; jdk-8u281-linux-x64.tar.gz -> jdk1.8.0_281
JDK_DIR="jdk1.8.0_${RELEASE_VERSION}"

MAINTAINER="Weasley J"
MAINTAINER_EMAIL="1432689025@qq.com"
IMAGE_NAME="jdk1.8"
IMAGE_VERSION="latest"
DOCKER_FILE="Dockerfile-jdk1.8"

# 切换工作目录
mkdir -pv ${WORKSPACE}
cd ${WORKSPACE} || exit

#
# 生成Dockerfile文件 - Dockerfile-jdk1.8
#
rm -rfv "${WORKSPACE}/${DOCKER_FILE}"
echo "FROM ubuntu:latest" >${WORKSPACE}/${DOCKER_FILE}
echo "MAINTAINER ${MAINTAINER} ${MAINTAINER_EMAIL}" >>${WORKSPACE}/${DOCKER_FILE}
echo "RUN mkdir -p ${WORKSPACE}" >>${WORKSPACE}/${DOCKER_FILE}
echo "WORKDIR ${WORKSPACE}" >>${WORKSPACE}/${DOCKER_FILE}
echo "ADD ${TAR_NAME} ${WORKSPACE}" >>${WORKSPACE}/${DOCKER_FILE}
echo "ENV JAVA_HOME ${WORKSPACE}/${JDK_DIR}/" >>${WORKSPACE}/${DOCKER_FILE}
echo "ENV JRE_HOME ${WORKSPACE}/${JDK_DIR}/jre" >>${WORKSPACE}/${DOCKER_FILE}
echo 'ENV PATH $JAVA_HOME/bin:$PATH' >>${WORKSPACE}/${DOCKER_FILE}
echo '#启动容器执行的命令，仅用于验证安装配置是否正确，生产环境使用需注释后再build' >>${WORKSPACE}/${DOCKER_FILE}
echo 'ENTRYPOINT ["java","-version"]' >>${WORKSPACE}/${DOCKER_FILE}

# 删除jdk1.8旧镜像
docker rmi -f ${IMAGE_NAME}:${IMAGE_VERSION}

# 执行Dockerfile文件，初次依赖镜像的时候会下载相应镜像，创建镜像
docker build -t ${IMAGE_NAME}:${IMAGE_VERSION} -f ${WORKSPACE}/${DOCKER_FILE} .

# 构建完成，删除生成Dockerfile文件，删除tar包
rm -rfv "${WORKSPACE}/${DOCKER_FILE}" && rm -rfv "${WORKSPACE}/${TAR_NAME}"

# 创建容器，验证结果，删除jdk容器，然后我们的jdk镜像就创建好了
docker rm -f jdk8 && docker run -it --name jdk8 ${IMAGE_NAME}:${IMAGE_VERSION} bash && docker rm -f jdk8

```

校验结果：

![image-20201113153026694](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201113153026694.png)

### 2.2.2 安装jdk11

- 编写Dockerfile（Dockerfile-jdk11）

```shell
#!/usr/bin/env bash

# 定义变量
WORKSPACE="/usr/local/jdk"
# jdk的小版本号
RELEASE_VERSION="11.0.10"
# jdk压缩包的名称
TAR_NAME="jdk-${RELEASE_VERSION}_linux-x64_bin.tar.gz"
# 解压后jdk文件夹的名称; jdk-11.0.10_linux-x64_bin.tar.gz -> jdk-11.0.10
JDK_HOME="jdk-${RELEASE_VERSION}"

# 镜像相关变量
MAINTAINER="Weasley J"
MAINTAINER_EMAIL="1432689025@qq.com"
JDK_IMAGE="jdk11"
IMAGE_VERSION="latest"
DOCKER_FILE="Dockerfile-jdk11"

# 切换工作目录
mkdir -pv ${WORKSPACE} && cd ${WORKSPACE} || exit
#
# 生成Dockerfile文件： Dockerfile-jdk11
#
rm -rfv ${WORKSPACE}/${DOCKER_FILE}
echo "FROM ubuntu:latest" >>${WORKSPACE}/${DOCKER_FILE}
echo "MAINTAINER ${MAINTAINER} ${MAINTAINER_EMAIL}" >>${WORKSPACE}/${DOCKER_FILE}
echo "RUN mkdir ${WORKSPACE}" >>${WORKSPACE}/${DOCKER_FILE}
echo "WORKDIR ${WORKSPACE}" >>${WORKSPACE}/${DOCKER_FILE}
echo "ADD ${TAR_NAME} ${WORKSPACE}" >>${WORKSPACE}/${DOCKER_FILE}
echo "ENV JAVA_HOME ${WORKSPACE}/${JDK_HOME}/" >>${WORKSPACE}/${DOCKER_FILE}
echo "ENV CLASSPATH ${WORKSPACE}/${JDK_HOME}/lib" >>${WORKSPACE}/${DOCKER_FILE}
echo 'ENV PATH=$JAVA_HOME/bin:$PATH' >>${WORKSPACE}/${DOCKER_FILE}
echo "#启动容器执行的命令，仅用于验证安装配置是否正确，生产环境使用需注释后再build" >>${WORKSPACE}/${DOCKER_FILE}
echo 'ENTRYPOINT ["java","-version"]' >>${WORKSPACE}/${DOCKER_FILE}

# 删除旧镜像
docker rmi -f ${JDK_IMAGE}:${IMAGE_VERSION}

# 执行Dockerfile文件，初次依赖镜像的时候会下载相应镜像，创建镜像
docker build -t ${JDK_IMAGE}:${IMAGE_VERSION} -f ${WORKSPACE}/${DOCKER_FILE} .

# 构建完成，删除生成Dockerfile文件，删除tar包
rm -rfv "${WORKSPACE}/${DOCKER_FILE}" && rm -rfv "${WORKSPACE}/${TAR_NAME}"

# 创建容器，验证结果，删除jdk容器，然后我们的jdk镜像就创建好了
docker rm -f jdk11 && docker run -it --name jdk11 ${JDK_IMAGE}:${IMAGE_VERSION} bash && docker rm -f jdk11

```

结果校验：

![image-20201113154651943](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201113154651943.png)

## 2.2.a Docker安装portainer图形化界面

> 介绍

Docker 图形化管理提供了很多工具，有Portainer、Docker UI、Shipyard等等，本文主要介绍Portainer。
Portainer是一个开源、轻量级Docker管理用户界面，基于Docker API，提供状态显示面板、应用模板快速部署、
容器镜像网络数据卷的基本操作（包括上传下载镜像，创建容器等操作）、事件日志显示、容器控制台操作、
Swarm集群和服务等集中管理和操作、登录用户管理和控制等功能。功能十分全面，基本能满足中小型单位对容器管理的全部需求。

> 安装

### 2.2.1 **下载官方镜像**

```shell
docker rmi portainer/portainer
docker pull portainer/portainer
```

### 2.2.2 **创建数据卷**

```shell
docker volume rm portainer_data
docker volume create portainer_data
```

### 2.2.3 运行容器

```shell
#!/usr/bin/env bash
docker stop portainer
docker rm -f portainer

docker run --name portainer \
  --restart=always \
  -p 9000:9000 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data/portainer/portainer \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -d portainer/portainer:latest
```

> **参数说明**
>
> -d #容器在后台运行
>
> --restart=always #docker服务启动后容器自启动
>
> -p 9000:9000 #宿主机9000端口映射容器中的9000端口
>
> -v portainer_data:/data #把宿主机portainer_data数据卷挂载到容器/data目录
>
> -v /etc/timezone:/etc/timezone \ #将宿主机的时区绑定到容器中，容器时区就会跟宿主机一样
>
> -v /etc/localtime:/etc/localtime \ #将宿主机的时间绑定到容器中，容器时间就会跟宿主机一样

```shell
#把宿主机的Docker守护进程(Docker daemon)默认监听的Unix域套接字挂载到容器中
-v /var/run/docker.sock:/var/run/docker.sock
```

### 2.2.4 查看容器进程

```shell
docker ps -l
```

### 2.2.5 访问服务

访问方式：http://ip:9000 ，首次登录需要注册用户，给用户admin设置密码，
单机版本选择“Local"，点击Connect即可连接到本地docker，
注意：从上图可以看出，有提示需要挂载本地 /var/run/docker.socker与容器内的/var/run/docker.socker连接。
因此，在启动时必须指定该挂载文件。
进入后可以对容器、镜像、网络、数据卷等进行管理，

### 2.2.6 参考链接

官方网站：https://portainer.io/
官方文档：https://portainer.readthedocs.io/
演示网址：http://demo.portainer.io ；账号: admin, 密码: tryportainer

## 2.2.b Docker安装portainer-ce图形化界面

[portainer-ce镜像（带tag）](https://hub.docker.com/r/portainer/portainer-ce/tags?page=1&ordering=last_updated)

### 2.2.1 安装portainer-ce

[官方安装链接](https://www.portainer.io/installation/)

![image-20201110133028049](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201110133028049.png)

```shell
#!/usr/bin/env bash

current_version="2.19.4"
old_version="2.18.4"

docker stop portainer && docker rm -f portainer
docker rmi portainer/portainer-ce:${old_version}
docker pull portainer/portainer-ce:${current_version}

#创建挂载卷
docker volume rm portainer_data && docker volume create portainer_data

docker stop portainer && docker rm -f portainer
docker run --name=portainer --restart=always \
  -p 8000:8000 -p 9000:9000 -p 9443:9443 \
  -v portainer_data:/data \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /usr/bin/docker:/usr/bin/docker \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -d portainer/portainer-ce:${current_version}

#日志
clear && docker logs -f portainer
```

### 2.2.2 浏览器访问容器9000端口

- You'll just need to access the port 9000 of the Docker engine where portainer is running using your browser.

- portainer-ce和portainer的区别：前者是后者的升级版
- portainer-ce的密码设置参照**2.2.A Docker安装portainer图形化界面**

http://192.168.40.132:9000

![image-20201110133818826](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201110133818826.png)

## 2.3 Docker安装RabbitMQ

### 2.3.1 拉取Rabbitmq镜像

```shell
#!/usr/bin/env bash

#1. 拉取镜像,选配有控制台的

# 镜像配有控制台（选这个）
docker pull rabbitmq:management

# 2. 运行rabbitmq
# 删除老容器
docker stop rabbitmq && docker rm -f rabbitmq

# 创建资料目录挂载数据
mkdir -pv /usr/local/rabbitmq
chmod 0777 -vR /usr/local/rabbitmq

# 创建并运行容器
docker run --name rabbitmq \
  --restart=always \
  -p 5672:5672 \
  -p 15672:15672 \
  -e RABBITMQ_DEFAULT_USER=admin \
  -e RABBITMQ_DEFAULT_PASS=123456 \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -v /usr/local/rabbitmq:/var/lib/rabbitmq \
  -d rabbitmq:management

#日志
clear && docker logs -f rabbitmq

# 参数备注:
# 将宿主机的时区与时间绑定到容器中，这样时间就会跟宿主机一样了。
# /etc/timezone 时区
# /etc/localtime 时间
# -v /etc/timezone:/etc/timezone \ #将宿主机的时区绑定到容器中，容器时区就会跟宿主机一样
# -v /etc/localtime:/etc/localtime \ #将宿主机的时间绑定到容器中，容器时间就会跟宿主机一样
# 5672: rabbitMQ的服务端口;
# 15672: RabbitMQ的控制台端口;
# 访问控制台需要使用15672, 通过程序连接RabbitMQ需要使用5672端口;

# 2.1 添加防火墙端口通行
firewall-cmd --zone=public --add-port=5672/tcp --permanent
firewall-cmd --zone=public --add-port=15672/tcp --permanent
firewall-cmd --reload
firewall-cmd --list-ports

```

### 2.3.2. 查看RabbitMQ容器进程信息

```shell
docker ps -a
```

![image-20201105004117835](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201105004117835.png)

```shell
docker top rabbitmq
```

![image-20201105004206369](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201105004206369.png)

### 2.3.4 浏览器登录RabbitMQ管理界面

- 启动容器后，可以浏览器中访问http://虚拟机或者服务器ip:15672/ 来查看控制台信息
- RabbitMQ默认的用户名：guest，密码：guest
- 这里使用创建RabbitMQ容器时指定的用户名:admin, 密码: 123456

- 浏览器中访问，启动RabbitMQ UI界面:
  http://192.168.40.132:15672/

## 2.4 Docker安装springcloud链路追踪zipkin

### 2.4.1 先安装数据库

> 这里采用`MySQL`

- 创建数据库

```mysql
DROP DATABASE IF EXISTS `zipkin`;
CREATE DATABASE `zipkin` CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_general_ci';
USE `zipkin`;
CREATE TABLE IF NOT EXISTS zipkin_spans
(
    `trace_id_high`       BIGINT       NOT NULL DEFAULT 0 COMMENT 'If non zero, this means the trace uses 128 bit traceIds instead of 64 bit',
    `trace_id`            BIGINT       NOT NULL,
    `id`                  BIGINT       NOT NULL,
    `name`                VARCHAR(255) NOT NULL,
    `remote_service_name` VARCHAR(255),
    `parent_id`           BIGINT,
    `debug`               BIT(1),
    `start_ts`            BIGINT COMMENT 'Span.timestamp(): epoch micros used for endTs query and to implement TTL',
    `duration`            BIGINT COMMENT 'Span.duration(): micros used for minDuration and maxDuration query',
    PRIMARY KEY (`trace_id_high`, `trace_id`, `id`)
) ENGINE = InnoDB
  ROW_FORMAT = COMPRESSED
  CHARACTER SET = utf8
  COLLATE utf8_general_ci;

ALTER TABLE zipkin_spans
    ADD INDEX (`trace_id_high`, `trace_id`) COMMENT 'for getTracesByIds';
ALTER TABLE zipkin_spans
    ADD INDEX (`name`) COMMENT 'for getTraces and getSpanNames';
ALTER TABLE zipkin_spans
    ADD INDEX (`remote_service_name`) COMMENT 'for getTraces and getRemoteServiceNames';
ALTER TABLE zipkin_spans
    ADD INDEX (`start_ts`) COMMENT 'for getTraces ordering and range';

CREATE TABLE IF NOT EXISTS zipkin_annotations
(
    `trace_id_high`         BIGINT       NOT NULL DEFAULT 0 COMMENT 'If non zero, this means the trace uses 128 bit traceIds instead of 64 bit',
    `trace_id`              BIGINT       NOT NULL COMMENT 'coincides with zipkin_spans.trace_id',
    `span_id`               BIGINT       NOT NULL COMMENT 'coincides with zipkin_spans.id',
    `a_key`                 VARCHAR(255) NOT NULL COMMENT 'BinaryAnnotation.key or Annotation.value if type == -1',
    `a_value`               BLOB COMMENT 'BinaryAnnotation.value(), which must be smaller than 64KB',
    `a_type`                INT          NOT NULL COMMENT 'BinaryAnnotation.type() or -1 if Annotation',
    `a_timestamp`           BIGINT COMMENT 'Used to implement TTL; Annotation.timestamp or zipkin_spans.timestamp',
    `endpoint_ipv4`         INT COMMENT 'Null when Binary/Annotation.endpoint is null',
    `endpoint_ipv6`         BINARY(16) COMMENT 'Null when Binary/Annotation.endpoint is null, or no IPv6 address',
    `endpoint_port`         SMALLINT COMMENT 'Null when Binary/Annotation.endpoint is null',
    `endpoint_service_name` VARCHAR(255) COMMENT 'Null when Binary/Annotation.endpoint is null'
) ENGINE = InnoDB
  ROW_FORMAT = COMPRESSED
  CHARACTER SET = utf8
  COLLATE utf8_general_ci;

ALTER TABLE zipkin_annotations
    ADD UNIQUE KEY (`trace_id_high`, `trace_id`, `span_id`, `a_key`, `a_timestamp`) COMMENT 'Ignore insert on duplicate';
ALTER TABLE zipkin_annotations
    ADD INDEX (`trace_id_high`, `trace_id`, `span_id`) COMMENT 'for joining with zipkin_spans';
ALTER TABLE zipkin_annotations
    ADD INDEX (`trace_id_high`, `trace_id`) COMMENT 'for getTraces/ByIds';
ALTER TABLE zipkin_annotations
    ADD INDEX (`endpoint_service_name`) COMMENT 'for getTraces and getServiceNames';
ALTER TABLE zipkin_annotations
    ADD INDEX (`a_type`) COMMENT 'for getTraces and autocomplete values';
ALTER TABLE zipkin_annotations
    ADD INDEX (`a_key`) COMMENT 'for getTraces and autocomplete values';
ALTER TABLE zipkin_annotations
    ADD INDEX (`trace_id`, `span_id`, `a_key`) COMMENT 'for dependencies job';

CREATE TABLE IF NOT EXISTS zipkin_dependencies
(
    `day`         DATE         NOT NULL,
    `parent`      VARCHAR(255) NOT NULL,
    `child`       VARCHAR(255) NOT NULL,
    `call_count`  BIGINT,
    `error_count` BIGINT,
    PRIMARY KEY (`day`, `parent`, `child`)
) ENGINE = InnoDB
  ROW_FORMAT = COMPRESSED
  CHARACTER SET = utf8
  COLLATE utf8_general_ci;

```

### 2.4.2 开始docker安装

```shell
#!/usr/bin/env bash

#搜索镜像
docker search zipkin

#不使用标签，直接拉去最新版
docker pull openzipkin/zipkin

#创建挂载卷目录,映射到本地
rm -rf /usr/local/zipkin
mkdir -p /usr/local/zipkin

#先销毁容器
docker stop zipkin
docker rm -f zipkin

#启动容器-数据库连接信息要修改
docker run --name zipkin \
  --restart=always \
  -p 9411:9411 \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -e STORAGE_TYPE=mysql \
  -e MYSQL_DB=zipkin \
  -e MYSQL_HOST=192.168.1.142 \
  -e MYSQL_PORT=3306 \
  -e MYSQL_USER=root \
  -e MYSQL_PASS=123456 \
  -d openzipkin/zipkin
```

### 2.4.3 浏览器访问验证

- 浏览器访问验证, ip自行修改,访问不了, 注意防火强是否打开
  http://192.168.1.142:9411/

## 2.5 Docker安装ElasticSearch

> 提示
>
> Elasticsearch 分web（9200）和tcp（9300）两种对外服务接口；
>
> 检查ElasticSearch的web服务运行情况，通过宿主机的IP访问，如：
>
>  http://宿主机IP:9200/

### 2.5.1  查看镜像版本号

- 这里以`elasticsearch 7.17.6`做安装示例，具体版本请移步[hub.docker.com](https://hub.docker.com/)查看

```shell
#!/bin/bash

#
#  定义全局安装变量
#

# 当前软件的版本
CURRENT_VERSION="7.17.6"
# 上一个版本
OLD_VERSION="7.16.3"
# 容器名称
CONTAINER_NAME="elasticsearch"
# 配置文件名称
CONFIG_FILE="elasticsearch.yml"
# ELK的基础安装目录
ELK_BASE_DIR="/usr/local/elk"
# 当前软件的安装目录
BASE_DIR="${ELK_BASE_DIR}/${CONTAINER_NAME}"

# 销毁旧容器
clear && docker stop ${CONTAINER_NAME} && docker rm -f ${CONTAINER_NAME}

docker rmi ${CONTAINER_NAME}:${OLD_VERSION}
docker pull ${CONTAINER_NAME}:${CURRENT_VERSION}
clear && printf '\r\t\t\t\t%s\n\r\r' "Docker镜像列表" && docker images

# 创建基础目录
mkdir -pv ${BASE_DIR}/{config,data,logs,plugins}

# 创建临时容器复制点配置文件出来，后期修改方便
docker run --name ${CONTAINER_NAME} \
  -p 9200:9200 \
  -p 9300:9300 \
  -e ES_JAVA_OPTS="-Xms512m -Xmx512m" \
  -d ${CONTAINER_NAME}:${CURRENT_VERSION}

# 容器:"/usr/share/elasticsearch/{config,plugins}" -> 宿主机:"/usr/local/elk/elasticsearch/{config,plugins}"
docker cp ${CONTAINER_NAME}:/usr/share/elasticsearch/config ${BASE_DIR}/
docker cp ${CONTAINER_NAME}:/usr/share/elasticsearch/plugins ${BASE_DIR}/

clear && printf '\r\t\t\t\t%s\n\r\r' "目录列表"
ll ${BASE_DIR}/config && ll ${BASE_DIR}/plugins

# 创建配置文件
rm -rfv ${BASE_DIR}/config/${CONFIG_FILE}
sudo tee ${BASE_DIR}/config/${CONFIG_FILE} <<-'EOF'
# ======================== Elasticsearch Configuration =========================
#
# NOTE: Elasticsearch comes with reasonable defaults for most settings.
#       Before you set out to tweak and tune the configuration, make sure you
#       understand what are you trying to accomplish and the consequences.
#
# The primary way of configuring a node is via this file. This template lists
# the most important settings you may want to configure for a production cluster.
#
# Please consult the documentation for further information on configuration options:
# https://www.elastic.co/guide/en/elasticsearch/reference/index.html
#
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
#cluster.name: my-application
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
#node.name: node-1
#
# Add custom attributes to the node:
#
#node.attr.rack: r1
#
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
#path.data: /path/to/data
#
# Path to log files:
#
#path.logs: /path/to/logs
#
# ----------------------------------- Memory -----------------------------------
#
# Lock the memory on startup:
#
#bootstrap.memory_lock: true
#
# Make sure that the heap size is set to about half the memory available
# on the system and that the owner of the process is allowed to use this
# limit.
#
# Elasticsearch performs poorly when the system is swapping the memory.
#
# ---------------------------------- Network -----------------------------------
#
# Set the bind address to a specific IP (IPv4 or IPv6):
#
#network.host: 192.168.0.1
#
# Set a custom port for HTTP:
#
#http.port: 9200
#
# For more information, consult the network module documentation.
#
# --------------------------------- Discovery ----------------------------------
#
# Pass an initial list of hosts to perform discovery when this node is started:
# The default list of hosts is ["127.0.0.1", "[::1]"]
#
#discovery.seed_hosts: ["host1", "host2"]
#
# Bootstrap the cluster using an initial set of master-eligible nodes:
#
#cluster.initial_master_nodes: ["node-1", "node-2"]
#
# For more information, consult the discovery and cluster formation module documentation.
#
# ---------------------------------- Gateway -----------------------------------
#
# Block initial recovery after a full cluster restart until N nodes are started:
#
#gateway.recover_after_nodes: 3
#
# For more information, consult the gateway module documentation.
#
# ---------------------------------- Various -----------------------------------
#
# Require explicit names when deleting indices:
#
#action.destructive_requires_name: true

#
# added by user
#

node.name: node-1
network.host: 0.0.0.0
http.cors.enabled: true
http.cors.allow-origin: "*"
EOF

#
# 修改目录权限
#
chmod -vR 777 ${BASE_DIR}/

# 删除旧版本的ik分词器如果存在
rm -rfv ${BASE_DIR}/plugins/analysis-ik
rm -rfv ${BASE_DIR}/logs/*.*

# 1. 创建单节点容器-开发环境推荐
clear && docker stop ${CONTAINER_NAME} && docker rm -f ${CONTAINER_NAME}
docker run --name ${CONTAINER_NAME} --restart=always \
  -p 9200:9200 \
  -p 9300:9300 \
  --net mynet \
  -e ES_JAVA_OPTS="-Xms512m -Xmx512m" \
  -e "discovery.type=single-node" \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -v ${BASE_DIR}/config/:/usr/share/elasticsearch/config \
  -v ${BASE_DIR}/data:/usr/share/elasticsearch/data \
  -v ${BASE_DIR}/logs:/usr/share/elasticsearch/logs \
  -v ${BASE_DIR}/plugins:/usr/share/elasticsearch/plugins \
  -d ${CONTAINER_NAME}:${CURRENT_VERSION}

# 2. 创建集群容器-生产环境推荐
clear && docker stop ${CONTAINER_NAME} && docker rm -f ${CONTAINER_NAME}
docker run --name ${CONTAINER_NAME} --restart=always \
  -p 9200:9200 \
  -p 9300:9300 \
  --net mynet \
  -e ES_JAVA_OPTS="-Xms1g -Xmx1g" \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -v ${BASE_DIR}/config/:/usr/share/elasticsearch/config \
  -v ${BASE_DIR}/data:/usr/share/elasticsearch/data \
  -v ${BASE_DIR}/logs:/usr/share/elasticsearch/logs \
  -v ${BASE_DIR}/plugins:/usr/share/elasticsearch/plugins \
  -d ${CONTAINER_NAME}:${CURRENT_VERSION}

# 查看日志
clear && docker logs -f elasticsearch

# 使用curl命令在服务器终端上验证下，有json返回表示安装成功了
curl http://127.0.0.1:9200
```

> 参数说明
>
> --name elasticsearch：将容器命名为 elasticsearch
> --restart=always: docker服务启动后启动此容器
> -p 9200:9200：将容器的9200端口映射到宿主机9200端口
> -p 9300:9300：将容器的9300端口映射到宿主机9300端口，目的是集群互相通信
> -e "discovery.type=single-node"：单例模式，开发环境推荐
> -e ES_JAVA_OPTS="-Xms64m -Xmx128m"：配置JVM内存大小
> -v /usr/local/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml：将配置文件挂载到宿主机
> -v /usr/local/elasticsearch/data:/usr/share/elasticsearch/data：将数据文件夹挂载到宿主机
> -v /usr/local/elasticsearch/logs:/usr/share/elasticsearch/logs:
> 将elasticsearch容器的logs文件夹挂载到宿主机/usr/local/elasticsearch/logs
> -v /usr/local/elasticsearch/plugins:/usr/share/elasticsearch/plugins： 将插件目录挂载到宿主机(需重启)
> -d elasticsearch:7.10.1：后台运行容器，并返回容器ID
>
>
>
> curl http://127.0.0.1:9200 返回示例
>
> ```json
> {
>   "name" : "node-1",
>   "cluster_name" : "elasticsearch",
>   "cluster_uuid" : "iDpemfw-RRCgJ__5tUmu6Q",
>   "version" : {
>     "number" : "7.10.1",
>     "build_flavor" : "default",
>     "build_type" : "docker",
>     "build_hash" : "1c34507e66d7db1211f66f3513706fdf548736aa",
>     "build_date" : "2020-12-05T01:00:33.671820Z",
>     "build_snapshot" : false,
>     "lucene_version" : "8.7.0",
>     "minimum_wire_compatibility_version" : "6.8.0",
>     "minimum_index_compatibility_version" : "6.0.0-beta1"
>   },
>   "tagline" : "You Know, for Search"
> }
> ```

### 2.5.2 查看docker进程

```shell
docker top elasticsearch
```

![image-20201105010415199](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201105010415199.png)

### 2.5.3 浏览器访问是否成功

> **ip改成自己的**

- http://192.168.40.132:9200

![image-20210301232823616](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210301232823616.png)

### 2.5.4 安装ik分词器

> 在线安装

```shell
#1. 进入到容器里
docker exec -it elasticsearch bash

VERSION="7.17.6"
#2. 执行安装命令，注意版本和你的ES版本对应
elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v${VERSION}/elasticsearch-analysis-ik-${VERSION}.zip

exit
#2.1. 退出容器
#3. 重启elasticsearch容器
docker restart elasticsearch && docker logs -f elasticsearch

#4. 使用curl命令在服务器终端上验证分词器是否安装成功
curl http://127.0.0.1:9200/_cat/plugins/
```

### 2.5.5 安装[elasticsearch-head](http://mobz.github.io/elasticsearch-head)

**Elasticsearch集群的Web前端**：

- Github链接: [elasticsearch-head](http://mobz.github.io/elasticsearch-head)

- 克隆仓库

```shell
BASE_DIR="/usr/local/elk"
mkdir -pv ${BASE_DIR} && cd ${BASE_DIR}
#克隆仓库
git clone git://github.com/mobz/elasticsearch-head.git
#切换到elasticsearch-head文件夹
cd elasticsearch-head
```

- 把第`10`行`RUN npm install`改为: `RUN npm install --registry=https://registry.npm.taobao.org`,完整的`Dockerfile`文件如下：

```shell
#
# 创建Dockerfile文件
#
BASE_DIR="/usr/local/elk/elasticsearch-head"
cd ${BASE_DIR}
CONFIG_FILE="${BASE_DIR}/Dockerfile"
rm -rfv ${CONFIG_FILE}
sudo tee ${CONFIG_FILE} <<-'EOF'
FROM node
MAINTAINER Niko Bellic <niko.bellic@kakaocorp.com>

RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app

RUN npm install --registry=https://registry.npm.taobao.org -g grunt

COPY package.json /usr/src/app/package.json
RUN npm install --registry=https://registry.npm.taobao.org

COPY . /usr/src/app

EXPOSE 9100

CMD grunt server
EOF
```

编辑完保存并退出vim编辑器

- 构建`elasticsearch-head:latest`镜像并创建容器

```shell
BASE_DIR="/usr/local/elk/elasticsearch-head"
cd ${BASE_DIR}
#构建镜像elasticsearch-head:latest,这个过程可能会有点慢,等待...
docker build -t elasticsearch-head:latest .

#创建容器
docker run --name elasticsearch-head --restart=always \
-p 9100:9100 \
-v /etc/timezone:/etc/timezone \
-v /etc/localtime:/etc/localtime \
-d elasticsearch-head:latest

# logs
docker logs -f elasticsearch-head

#清空缓存文件夹
rm -rfv ${BASE_DIR}
```

- 查看`elasticsearch-head`进程信息

```shell
docker top elasticsearch-head
```

![image-20201108160305656](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201108160305656.png)

```shell
docker ps -a
```

![image-20201108160426335](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201108160426335.png)

- 浏览器访问`elasticsearch-head`的前端页面

http://192.168.40.132:9100

输入`elasticsearch`的`ip:9200`查看`ES`集群连接信息:

![image-20201108160557957](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201108160557957.png)

### 2.5.6 ES集群docker-compose示例

To get a three-node Elasticsearch cluster up and running in Docker, you can use Docker Compose:

1. Create a `docker-compose.yml` file:

```yaml
version: '2.2'
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.17.6
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data01:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - elastic
  es02:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.17.6
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data02:/usr/share/elasticsearch/data
    networks:
      - elastic
  es03:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.17.6
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data03:/usr/share/elasticsearch/data
    networks:
      - elastic

volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  elastic:
    driver: bridge
```

## 2.6.a Docker安装Kibana

> 注意：版本最好和ElasticSearch的版本一致

```shell
#!/bin/bash

#
#  *** 定义全局安装变量 ***
#

# 当前软件的版本
CURRENT_VERSION="7.17.6"
# 上一个版本
OLD_VERSION="7.10.1"
# 容器名称
CONTAINER_NAME="kibana"
# 配置文件名称
CONFIG_FILE="kibana.yml"
# ELK的基础安装目录
ELK_BASE_DIR="/usr/local/elk"
# 当前软件的安装目录
BASE_DIR="${ELK_BASE_DIR}/${CONTAINER_NAME}"

# 销毁旧容器
docker stop ${CONTAINER_NAME} && docker rm -f ${CONTAINER_NAME}

docker rmi ${CONTAINER_NAME}:${OLD_VERSION}
docker pull ${CONTAINER_NAME}:${CURRENT_VERSION}
clear && printf '\r\t\t\t\t%s\n\r\r' "Docker镜像列表" && docker images

# 创建基础目录
mkdir -pv ${BASE_DIR}/{config,data,logs,plugins}

# 创建临时容器复制点配置文件出来，后期修改方便
docker run --name ${CONTAINER_NAME} \
  -p 5601:5601 \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -d ${CONTAINER_NAME}:${CURRENT_VERSION}

# 把容器中的"/usr/share/kibana/{config,plugins}" -> 宿主机"/usr/local/elk/kibana/{config,plugins}"
docker cp ${CONTAINER_NAME}:/usr/share/${CONTAINER_NAME}/config ${BASE_DIR}/
docker cp ${CONTAINER_NAME}:/usr/share/${CONTAINER_NAME}/plugins ${BASE_DIR}/

clear && printf '\r\t\t\t\t%s\n\r\r' "目录列表"
ll ${BASE_DIR}/config && ll ${BASE_DIR}/plugins

#
# 创建配置文件kibana.yml，配置文件中连接elasticsearch的ip要根据自己的实际情况修改
#

rm -rfv ${BASE_DIR}/config/${CONFIG_FILE}
sudo tee ${BASE_DIR}/config/${CONFIG_FILE} <<-'EOF'
#
# ** THIS IS AN AUTO-GENERATED FILE **
#

# Default Kibana configuration for docker target

#
# added by user
# 更多设置参考官网: https://www.elastic.co/guide/en/kibana/current/settings.html
#

# 主机名
server.name: "kibana"

# 主机地址，可以是ip
server.host: "0"

# kibana访问es服务器的URL，就可以有多个，以英文逗号","隔开
elasticsearch.hosts: ["http://192.168.40.132:9200/"]

monitoring.ui.container.elasticsearch.enabled: true

# 修改kibana可视化界面语言为中文，默认是英文界面
i18n.locale: "zh-CN"

# 在代理后面运行，则可以指定安装Kibana的路径
# 使用server.rewriteBasePath设置告诉Kibana是否应删除basePath
# 接收到的请求，并在启动时防止过时警告，此设置不能以斜杠结尾
# server.basePath: "/kibana"
EOF

clear && cat ${BASE_DIR}/config/${CONFIG_FILE}

# 销毁旧容器
docker stop ${CONTAINER_NAME} && docker rm -f ${CONTAINER_NAME}

#运行容器; --restart=always 表示：docker启动容器后自动启动该容器
docker run --name ${CONTAINER_NAME} --restart=always \
  -p 5601:5601 \
  --net mynet \
  -e JAVA_OPTS="-Xm512m -Xmx512m" \
  -v ${BASE_DIR}/config:/usr/share/kibana/config \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -d ${CONTAINER_NAME}:${CURRENT_VERSION}

# 查看日志
clear && docker logs -f kibana

```

### 2.6.1 查看docker进程

```shell
docker ps -a
docker top kibana
```

### 2.6.2 访问kibana界面

> 浏览器访问

http://192.168.40.132:5601

![image-20210302001528750](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210302001528750.png)

## 2.6.b Docker安装Logstash

> 注意：版本最好和`ElasticSearch`,`Kibana`的版本保持一致

### 2.6.3  拉取镜像准备工作目录

```shell
#!/bin/bash

#
#  *** 定义全局安装变量 ***
#

# 当前软件的版本
CURRENT_VERSION="7.17.6"
# 上一个版本
OLD_VERSION="7.10.1"
# 容器名称
CONTAINER_NAME="logstash"
# 配置文件名称
CONFIG_FILE="${CONTAINER_NAME}.yml"
# ELK的基础安装目录
ELK_BASE_DIR="/usr/local/elk"
# 当前软件的安装目录
BASE_DIR="${ELK_BASE_DIR}/${CONTAINER_NAME}"

# 销毁旧容器
docker stop ${CONTAINER_NAME} && docker rm -f ${CONTAINER_NAME}

docker rmi ${CONTAINER_NAME}:${OLD_VERSION}
docker pull ${CONTAINER_NAME}:${CURRENT_VERSION}
clear && printf '\r\t\t\t\t%s\n\r\r' "Docker镜像列表" && docker images

# 创建基础目录
mkdir -pv ${BASE_DIR}/{config/conf.d,.gem,pipeline}

# 创建临时容器复制点配置文件出来，后期修改方便
docker run --name ${CONTAINER_NAME} \
  -p 5044:5044 \
  -p 9600:9600 \
  -d ${CONTAINER_NAME}:${CURRENT_VERSION}

# 把容器中的"/usr/share/logstash/{config,plugins}" -> 宿主机"/usr/local/elk/logstash/{config,plugins}"
docker cp ${CONTAINER_NAME}:/usr/share/${CONTAINER_NAME}/config ${BASE_DIR}/
docker cp ${CONTAINER_NAME}:/usr/share/${CONTAINER_NAME}/.gem ${BASE_DIR}/
docker cp ${CONTAINER_NAME}:/usr/share/${CONTAINER_NAME}/pipeline ${BASE_DIR}/

clear && printf '\r\t\t\t\t%s\n\r\r' "目录列表"
ll ${BASE_DIR}/config && ll ${BASE_DIR}/pipeline && ll ${BASE_DIR}/.gem

#
# 创建配置文件logstash.yml，配置文件中连接elasticsearch的ip要根据自己的实际情况修改
#
CONFIG_FILE_NAME="${BASE_DIR}/config/${CONFIG_FILE}"
rm -rfv ${CONFIG_FILE_NAME}
sudo tee ${CONFIG_FILE_NAME} <<-'EOF'
#
#    added by user
#

# 0.0.0.0：允许任何IP访问
http.host: "0.0.0.0"

# 配置elasticsearch集群地址
xpack.monitoring.elasticsearch.hosts: [ "http://192.168.40.132:9200" ]

# 允许监控
xpack.monitoring.enabled: true

# 启动时读取配置文件指定
path.config: /usr/share/logstash/conf.d/*.conf
path.logs: /usr/share/logstash/logs
EOF

#
# 新建文件 logstash.conf，用来收集/var/log/messages
#
CONFIG_FILE_NAME="${BASE_DIR}/config/conf.d/logstash.conf"
rm -rfv ${CONFIG_FILE_NAME}
sudo tee ${CONFIG_FILE_NAME} <<-'EOF'
# Sample Logstash configuration for creating a simple
# Beats -> Logstash -> Elasticsearch pipeline.

input {

  file {
    #标签
    type => "logstash-dev"
    #采集点
    path => "/var/log/messages"
    #开始收集点
    start_position => "beginning"
    #扫描间隔时间，默认是1s，建议5s
    stat_interval => "5"
  }

  tcp {
    mode => "server"
    host => "0.0.0.0"
    port => "5044"
    codec => "json_lines"
  }
}

#请根据实际情况修改elasticsearch地址
output {

  elasticsearch {
    hosts => ["http://192.168.40.132:9200"]
    
    #此处index可以从logback-spring.xml中customFields标签中获取app_name和run_env的值做为索引的名称
    index => "%{[app_name]}-%{[run_env]}" #本地开发没必要每天生成一个文件, 生产环境放开使用下面的index
    #index => "%{[app_name]}-%{[run_env]}-%{+YYYY.MM.dd}"
    #index => index => "logstash-dev-%{+YYYY.MM.dd}"
    
    #user => "elastic"
    #password => "changeme"
  }

  #若不需要在控制台中输出，此行可以删除
  stdout {
    codec => rubydebug
  }
}
EOF


# 设置日志文件读取权限
chmod -vR 777 ${BASE_DIR}/
chmod -v 0644 /var/log/messages

# 销毁旧容器
docker stop ${CONTAINER_NAME} && docker rm -f ${CONTAINER_NAME}

#运行容器
docker run --name ${CONTAINER_NAME} --restart=always \
  -p 5044:5044 \
  -p 9600:9600 \
  --net mynet \
  --privileged=true \
  -e JAVA_OPTS="-Xm512m -Xmx512m" \
  -v ${BASE_DIR}/config/conf.d/:/usr/share/logstash/conf.d/ \
  -v ${BASE_DIR}/config:/usr/share/${CONTAINER_NAME}/config \
  -v ${BASE_DIR}/pipeline:/usr/share/${CONTAINER_NAME}/pipeline \
  -v ${BASE_DIR}/.gem:/usr/share/${CONTAINER_NAME}/.gem \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -d ${CONTAINER_NAME}:${CURRENT_VERSION}

# 查看日志
clear && docker logs -f logstash
```

### 2.6.4 进入logstash容器安装json插件

> 可能有点慢，等待即可

```bash
docker exec -it logstash bash

clear && logstash-plugin install logstash-codec-json_lines
clear && logstash-plugin install logstash-codec-json && exit

docker restart logstash && clear && docker logs -f logstash
```

![image-20210302052314933](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210302052314933.png)

### 2.6.5  修改Logstash的JVM参数

```bash
#!/bin/bash

#
#  *** 定义全局安装变量 ***
#

# 当前软件的版本
CURRENT_VERSION="7.17.6"
# 上一个版本
OLD_VERSION="7.16.3"
# 容器名称
CONTAINER_NAME="logstash"
# 配置文件名称
CONFIG_FILE="${CONTAINER_NAME}.yml"
# ELK的基础安装目录
ELK_BASE_DIR="/usr/local/elk"
# 当前软件的安装目录
BASE_DIR="${ELK_BASE_DIR}/${CONTAINER_NAME}"

#
#       *** 修改默认的jvm参数 -- jvm.options ***
#
rm -rfv ${BASE_DIR}/config/jvm.options
sudo tee ${BASE_DIR}/config/jvm.options <<-'EOF'
## JVM configuration

# Xms represents the initial size of total heap space
# Xmx represents the maximum size of total heap space

# Xms Xmx默认为1g，调小一点，看自己的机器性能
-Xms512m
-Xmx512m

################################################################
## Expert settings
################################################################
##
## All settings below this section are considered
## expert settings. Don't tamper with them unless
## you understand what you are doing
##
################################################################

## GC configuration
-XX:+UseConcMarkSweepGC
-XX:CMSInitiatingOccupancyFraction=75
-XX:+UseCMSInitiatingOccupancyOnly

## Locale
# Set the locale language
#-Duser.language=en

# Set the locale country
#-Duser.country=US

# Set the locale variant, if any
#-Duser.variant=

## basic

# set the I/O temp directory
#-Djava.io.tmpdir=$HOME

# set to headless, just in case
-Djava.awt.headless=true

# ensure UTF-8 encoding by default (e.g. filenames)
-Dfile.encoding=UTF-8

# use our provided JNA always versus the system one
#-Djna.nosys=true

# Turn on JRuby invokedynamic
-Djruby.compile.invokedynamic=true
# Force Compilation
-Djruby.jit.threshold=0
# Make sure joni regexp interruptability is enabled
-Djruby.regexp.interruptible=true

## heap dumps

# generate a heap dump when an allocation from the Java heap fails
# heap dumps are created in the working directory of the JVM
-XX:+HeapDumpOnOutOfMemoryError

# specify an alternative path for heap dumps
# ensure the directory exists and has sufficient space
#-XX:HeapDumpPath=${LOGSTASH_HOME}/heapdump.hprof

## GC logging
#-XX:+PrintGCDetails
#-XX:+PrintGCTimeStamps
#-XX:+PrintGCDateStamps
#-XX:+PrintClassHistogram
#-XX:+PrintTenuringDistribution
#-XX:+PrintGCApplicationStoppedTime

# log GC status to a file with time stamps
# ensure the directory exists
#-Xloggc:${LS_GC_LOG_FILE}

# Entropy source for randomness
-Djava.security.egd=file:/dev/urandom

# Copy the logging context from parent threads to children
-Dlog4j2.isThreadContextMapInheritable=true
EOF

#充重启容器
docker restart logstash && clear && docker logs -f logstash
```

- 浏览器访问: http://127.0.0.1:9600/

```bash
curl http://127.0.0.1:9600/
```

![image-20210302045936822](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210302045936822.png)

## 2.7 Docker安装MySQL

- 下载`mysql`镜像

> 默认拉取的镜像是最新版

```shell
docker stop mysql && docker rm -f mysql
docker rmi -f mysql && docker pull mysql
```

- 停止和删除旧容器

```shell
docker stop mysql && docker rm -f mysql
```

- 创建挂在目录

```shell
# rm -rfv /usr/local/mysql
sudo mkdir -pv /usr/local/mysql/{conf,data,logs}
```

- 创建后台守护运行容器

> 注意自己容器映射宿主机的端口`-p 3306:3306 \`

```shell
# 创建一个共享网络
docker network create --driver bridge --subnet 172.18.0.0/16 --gateway 172.18.0.1 mynet

docker stop mysql && docker rm -f mysql
docker run --name mysql --restart=always \
  -p 3306:3306 \
  --net mynet \
  -e MYSQL_ROOT_PASSWORD=123456 \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -v /usr/local/mysql/data:/var/lib/mysql \
  -v /usr/local/mysql/conf:/etc/mysql/conf.d \
  -v /usr/local/mysql/logs:/logs \
  -d mysql:8.4.5
```

- 日志

```bash
clear && docker logs -f mysql
```

- 进入`MySQL`容器

```shell
docker exec -it mysql bash
```

- 登录容器内`MySQL`

```shell
mysql -u root -p123456

use mysql;

#添加远程登录用户
CREATE USER 'lwj'@'%' IDENTIFIED WITH caching_sha2_password BY '改成你的密码';
GRANT ALL PRIVILEGES ON *.* TO 'lwj'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;

#提示: 开发环境|本地使用: 建议开启root用户远程登陆访问, 生产环境不建议这样做
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
ALTER USER 'root'@'%' IDENTIFIED BY '123456';
FLUSH PRIVILEGES;
```

- 退出`MySQL`与`MySQL`容器

```shell
#退出mysql
exit;
#退出mysq容器
exit
```

- 使用Navicat连接测试docker容器内的mysql是否成功

![image-20201105105011529](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201105105011529.png)

## 2.8 Docker安装Redis

```shell
#!/bin/bash

# 基础目录
BASE_DIR="/usr/local/redis"

#停止和删除旧容器
docker stop redis && docker rm -f redis

# 删除旧镜像如果存在，拉取新镜像
docker rmi -f redis && docker pull redis

# 创建挂载目录
sudo mkdir -pv ${BASE_DIR}/{data,conf}
cd ${BASE_DIR}/conf

# 从官下载redis配置文件
rm -rfv ${BASE_DIR}/conf/*
wget http://download.redis.io/redis-stable/redis.conf
mv redis.conf redis.conf.bak
touch redis.conf

#修改权限
chmod -vR 777 ${BASE_DIR}/

# 安装redis
docker stop redis && docker rm -f redis
docker run --name redis --restart=always \
  -p 6379:6379 \
  -v ${BASE_DIR}/data/:/data \
  -v ${BASE_DIR}/conf/redis.conf:/etc/redis/redis.conf \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -d redis:latest \
  redis-server /etc/redis/redis.conf --appendonly yes --requirepass "123456"
```



- redis日志

```sh
clear && docker logs -f redis
```



> 参数说明：
>
> /etc/redis/redis.conf /etc/redis/redis.conf 关键配置，让redis以指定的配置文件启动，而不是默认无配置启动
>
> --appendonly yes redis启动后开启数据持久化
>
> --requirepass ”123456” 你的密码，根据情况设置

## 2.9 Docker安装Gitea

- 安装`docker-compose`

https://docs.docker.com/compose/install/

- [安装giteadocker 镜像](https://hub.docker.com/r/gitea/gitea)

1. [中文简介](https://docs.gitea.io/zh-cn/install-with-docker/)
2. [英文简介（推荐）](https://docs.gitea.io/en-us/install-with-docker/)

- 拉取镜像

```shell
docker pull gitea/gitea
docker pull mysql
```

- 创建并启动容器

> docker-compose file (with description):
> Use this docker-compose file to get up and running quickly. You should change mysql user- and root password. When
> installing, you can reference the database container as host db.
> This configuration will publicly expose ports 3000 and 22.
> To start Gitea in combination with a MySQL database, apply these changes to the docker-compose.yml file created above.

```shell
#创建挂载映射文件夹
sudo mkdir -pv /usr/local/gitea/data
sudo mkdir -pv /usr/local/mysql/{conf,data,logs}
#停止&删除旧容器
docker stop mysql
docker stop gitea
docker rm -f mysql
docker rm -f gitea

#创建docker-compose.yml
cd /usr/local/gitea
touch docker-compose.yml
vim docker-compose.yml
```

- 编辑`docker-compose.yml`文件内容如下

```yaml
version: "3"
networks:
  gitea:
    external: false
services:
  server:
    image: gitea/gitea:latest
    container_name: gitea
    environment:
      - USER_UID=1000
      - USER_GID=1000
      - DB_TYPE=mysql
      #数据库主机,我这里直接填服务器的(私网ip)172.19.170.239,默认的db:3306会出问题（如果3306端口已经被占用的情况下，无占用请忽略）
      - DB_HOST=172.19.170.239:3306
      - DB_NAME=gitea
      - DB_USER=root
      - DB_PASSWD=123456
    restart: always
    networks:
      - gitea
    volumes:
      - /usr/local/gitea/data:/data
      - /etc/timezone:/etc/timezone
      - /etc/localtime:/etc/localtime
    ports:
      - "3000:3000"
      #222端口是为了和主机的ssh的22端口分开
      - "222:22"
    depends_on:
      - db
  db:
    image: mysql:latest
    container_name: mysql
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=123456
      - MYSQL_USER=root
      - MYSQL_PASSWORD=123456
      - MYSQL_DATABASE=gitea
    networks:
      - gitea
    volumes:
      - /etc/timezone:/etc/timezone
      - /etc/localtime:/etc/localtime
      - /usr/local/mysql/data:/var/lib/mysql
      - /usr/local/mysql/conf:/etc/mysql/conf.d
      - /usr/local/mysql/logs:/logs
    ports:
      - "3306:3306"
```

- 运行`docker compose`

```shell
cd /usr/local/gitea && docker-compose up -d server
```

- 查看进程

```shell
cd /usr/local/gitea && docker-compose ps -a
```

- 开启`gitea`的`mysql`远程访问

```shell
#进入容器
docker exec -it mysql bash

#登录mysql
mysql -u root -p

#输入密码后
use mysql;
alter user 'root'@'%' identified with mysql_native_password by '123456';
flush privileges;

#添加远程登录用户(建议)
CREATE USER 'lwj'@'%' IDENTIFIED WITH mysql_native_password BY 'Liuwenjing123Abc!';
GRANT ALL PRIVILEGES ON *.* TO 'lwj'@'%';
FLUSH PRIVILEGES;

#退出mysql
exit;
#退出mysql容器
exit
```

- 浏览器访问`gitea`首页进行初始化设置

http://192.168.x.x:3000

> **提别提醒**：

登录->数据库主机: 填服务器的(私网ip)172.19.170.239:3306(根据你的yml文件里面映射实际情况填就行了)

创建管理员帐户是可选的。第一个注册用户将自动成为管理员。

> 设置管理员账号:

**管理员用户名**(推荐手机号): 1432689025
**密码**:yourPassword
**邮箱(**可用于登录): 1432689025@qq.com



> **亦可以使用nginx代理3000端口**
>
> nginx 配置如下：
>
> ```nginx
> # clear && /usr/local/nginx/sbin/nginx -s reload
> # clear && /usr/local/nginx/sbin/nginx -t
> 
> #user  nobody;
> worker_processes  1;
> 
> #error_log  logs/error.log;
> #error_log  logs/error.log  notice;
> #error_log  logs/error.log  info;
> 
> #pid        logs/nginx.pid;
> 
> 
> events {
>     worker_connections  1024;
> }
> 
> 
> http {
>     include       mime.types;
>     default_type  application/octet-stream;
> 
>     #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
>     #                  '$status $body_bytes_sent "$http_referer" '
>     #                  '"$http_user_agent" "$http_x_forwarded_for"';
> 
>     #access_log  logs/access.log  main;
> 
>     sendfile        on;
>     #tcp_nopush     on;
> 
>     #keepalive_timeout  0;
>     keepalive_timeout  65;
> 
>     #gzip  on;
> 
>     #解决"nginx could not build the server_names_hash"的方法
>     server_names_hash_bucket_size 64;
> 
>     server {
>         listen       80;
>         server_name  localhost 172.26.224.8;
> 
>         #charset koi8-r;
> 
>         #access_log  logs/host.access.log  main;
> 
>         #http --> https
>         #if ($scheme = http) {
>         #    return 301 https://$server_name$request_uri;
>         #}
>         #if ($ssl_protocol = "") {
>         #    return 301 https://$server_name$request_uri;
>         #}
> 
>         proxy_set_header Host $host;
>         proxy_set_header X-Forwarded-Host $host;
>         proxy_set_header X-Forwarded-Server $host;
>         proxy_set_header X-Real-IP $remote_addr;
>         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
>         proxy_set_header REMOTE-HOST $remote_addr;
>         proxy_set_header X-Forwarded-For $remote_addr; #设置请求源地址
>         proxy_set_header X-Forwarded-Proto $scheme; #设置Http协议
>         add_header X-Cache $upstream_cache_status;
> 
>         proxy_headers_hash_max_size 512;
>         proxy_headers_hash_bucket_size 128;
> 
>         #Set Nginx Cache
>         add_header Cache-Control no-cache;
> 
>         client_max_body_size 50m;
> 
>         #rabbitmq后台界面
>         location /rabbitmq/ {
>             proxy_pass http://localhost:15672;            
>             rewrite "^/rabbitmq/(.*)$" /$1 break;
> 	        proxy_connect_timeout 600;
>             proxy_read_timeout 600;
>         }
> 
>         #默认代理到git仓库
>         location / {
>             proxy_pass http://localhost:3000;
>             proxy_connect_timeout 600;
>             proxy_read_timeout 600;
>         }
> 
>         #error_page  404              /404.html;
> 
>         # redirect server error pages to the static page /50x.html
>         error_page   500 502 503 504  /50x.html;
>         location = /50x.html {
>             root   html;
>         }
>     }
> 
>     # another virtual host using mix of IP-, name-, and port-based configuration
>     #
>     #server {
>     #    listen       8000;
>     #    listen       somename:8080;
>     #    server_name  somename  alias  another.alias;
> 
>     #    location / {
>     #        root   html;
>     #        index  index.html index.htm;
>     #    }
>     #}
> 
> 
>     # HTTPS server
>     #
>     #server {
>     #    listen       443 ssl;
>     #    server_name  localhost;
> 
>     #    ssl_certificate      cert.pem;
>     #    ssl_certificate_key  cert.key;
> 
>     #    ssl_session_cache    shared:SSL:1m;
>     #    ssl_session_timeout  5m;
> 
>     #    ssl_ciphers  HIGH:!aNULL:!MD5;
>     #    ssl_prefer_server_ciphers  on;
> 
>     #    location / {
>     #        root   html;
>     #        index  index.html index.htm;
>     #    }
>     #}
> }
> ```
>
>

## 2.10 Docker安装Jenkins

[官方文档](https://github.com/jenkinsci/docker/blob/master/README.md)

### 2.10.1 `Jenkins`版本选择

```shell
#To use the latest LTS: 
docker pull jenkins/jenkins:lts-jdk11

#To use the latest weekly: 
docker pull jenkins/jenkins
```

### 2.10.2 使用`jenkins/jenkins:lts`

```shell
# 创建挂载卷目录,映射到本地
# rm -rfv /usr/local/{jenkins,jenkins/home}
sudo mkdir -pv /usr/local/{jenkins,jenkins/home,jenkins-script,maven,webapp,jdk,node,logs}/

#先销毁容器
docker stop jenkins
docker rm -f jenkins

#启动容器
docker run --name jenkins \
  --restart=always \
  -u root \
  -p 9090:8080 \
  -p 50000:50000 \
  -e JAVA_OPTS="-Xms512m -Xmx512m" \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -v /usr/local/jenkins:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /usr/bin/docker:/usr/bin/docker \
  -v /usr/local/jenkins/home:/home \
  -v /usr/local/jenkins-script:/usr/local/jenkins-script \
  -v /usr/local/webapp:/usr/local/webapp \
  -v /usr/local/maven:/usr/local/maven \
  -v /usr/local/logs:/usr/local/logs \
  -v /usr/local/jdk:/usr/local/jdk \
  -v /usr/local/node:/usr/local/node \
  -d jenkins/jenkins:lts-jdk11
```

- 以下挂载卷解决 docker in docker的问题

```shell
-v /var/run/docker.sock:/var/run/docker.sock \
-v /usr/bin/docker:/usr/bin/docker \
```

- 验证可以在Jenkins容器内部里面执行

```shell
docker exec -it jenkins bash
```

### 2.10.3 查看`Jenkins`首次初始化的登录密码

> *两种方式获取*

1. 进入docker容器查看docker初始化密码

```shell
docker exec -it jenkins bash
#复制cat出来的密码，用于登录jenkins的web管理页面
cat /var/jenkins_home/secrets/initialAdminPassword
```

2. 从jenkins的启动日志中查看密码

```shell
docker logs -f jenkins
```

你在服务器终端应该可以看到一串密文：

![image-20201105132744609](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201105132744609.png)

![image-20201105132844081](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201105132844081.png)



> Jenkins initial setup is required. An admin user has been created and a password generated.
> Please use the following password to proceed to installation:
>
> 30d6c86ecfc248eba6907f09584597b1
>
> This may also be found at: /var/jenkins_home/secrets/initialAdminPassword

`30d6c86ecfc248eba6907f09584597b1`就是你需要的密码

### 2.10.4 关闭高版本`Jenkins`的跨站请求伪造

[参考]( http://www.rhce.cc/2743.html)

- 参数

```java
-Dhudson.security.csrf.GlobalCrumbIssuerConfiguration.DISABLE_CSRF_PROTECTION=true
```

```shell
#进入jenkins容器
docker exec -it jenkins  bash

# 在容器里安装vim
apt-get -y update
apt-get -y install vim

# 修改文件
vim /usr/local/bin/jenkins.sh
```

> 未修改之前

![image-20201105133647941](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201105133647941.png)

- 使用`Shift+Ins`键把参数复制到指定位置

1. 复制好参数

```java
"-Dhudson.security.csrf.GlobalCrumbIssuerConfiguration.DISABLE_CSRF_PROTECTION=true"
```

*按照原来的格式，带引号吧，风格统一*

![image-20201105120617373](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201105120617373.png)

- 退出jenkins容器

```shell
exit
```

- 重启jenkins容器

```shell
docker restart jenkins && docker logs -f jenkins
```

### 2.10.5 登录Jenkins Web页面进行初始化设置

- 浏览器输入链接测试,自己的ip:9090

  http://192.168.40.132:9090

> 复制你的密码登陆就行了

![image-20201105134308869](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201105134308869.png)

至此，`Jenkins`的的大部分配置已完毕，你可以尽情的搭建你的企业级`DevOps`了......

### 2.10.6 配置换机Jenkins的Maven环境

JDK的比较简单，**Maven的比较坑记录一下**

- 全局工具配置

![image-20221024000319560](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20221024000319560.png)

- 系统管理/全局属性/环境变量

1. JAVA_HOME

2. MAVEN_HOME

3. PATH+EXTRA

![image-20221024002156868](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20221024002156868.png)

### 2.10.7 `Jenkins`基于[Role-based Authorization Strategy](https://plugins.jenkins.io/role-strategy)和[Authorize Project](https://plugins.jenkins.io/authorize-project)角色权限分配后`git webhooks`
**远程构建触发**

> 踩坑背景：
>
>  Jenkins使用[Role-based Authorization Strategy](https://plugins.jenkins.io/role-strategy)
> 和[Authorize Project](https://plugins.jenkins.io/authorize-project)
>
角色权限管理策略后，默认的Git远程触发构建会被权限插件拦截，导致我们提交代码到Git仓库后，Git发送POST请求给Jenkins服务器，Jenkins服务器响应403状态码，所以我们要解决这个问题，实现基于权限策略的Git提交代码后自动构建发布我们的项目到远程测试服务器。

#### 2.10.6.1 权限入口

![image-20201112153515341](Docker及Docker插件安装.assets/image-20201112153515341.png)

![image-20201112153632683](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112153632683.png)

![image-20201112153733616](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112153733616.png)

![image-20201112153756205](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112153756205.png)

#### 2.10.6.2 分配权限

![image-20201112153837288](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112153837288.png)

![image-20201112153908509](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112153908509.png)

#### 2.10.6.3 生成api凭据token

![image-20201112154054416](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112154054416.png)

![image-20201112154329572](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112154329572.png)

![image-20201112154443226](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112154443226.png)





> 立刻拷贝该 Token，因为以后将无法获取它
>
> 11b474f73eb6e9c5e0d7808c606c05b2f9

![image-20201112154922681](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112154922681.png)

点击保存。

#### 2.10.6.4 配置Git Web钩子

Use the following URL to trigger build remotely:

`JENKINS_URL`/job/recommendation_web_manage/build?token=`TOKEN_NAME` 或者/buildWithParameters?token=`TOKEN_NAME`
Optionally append `&cause=Cause+Text` to provide text that will be included in the recorded build cause.

- 基于权限控制的远程webhooks的目标URL模板

URL模板：

```http
http://jenkins管理员用户名:身份验证token令牌@Jenkins主机ip:端口/job/需要git出发自动构建的任务名/build?token=身份验证token令牌
```

URL示例:

```http
http://alphahub:116226d0b77a83cd3fac6bf32bc8bddf31@106.14.13.156:9090/job/recommendation_web_portal/build?token=116226d0b77a83cd3fac6bf32bc8bddf31
```

- 打开出发构建项目的git链接

![image-20201112160226446](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112160226446.png)

- 点击仓库设置

![image-20201112160256024](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112160256024.png)

![image-20201112160331754](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112160331754.png)

![image-20201112160354538](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112160354538.png)

> 这里添加web钩子选git就好了，我们要实现的是：提交代码到git仓库，Jenkins自动构建项目发布到测试环境。

![image-20201112160818870](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112160818870.png)

![image-20201112160950558](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112160950558.png)

![image-20201112161800191](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112161800191.png)

#### 2.10.6.5 浏览器新打开一个Jenkins的窗口验证

##### a. 打开两个窗口

![image-20201112162252275](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112162252275.png)

##### b. 点击测试推送

![image-20201112162615150](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112162615150.png)

![image-20201112162723187](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112162723187.png)



> 从Jenkins的构建日志我们可以发现此次自动构建是成功的：

![image-20201112162949836](https://super-power-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201112162949836.png)

这就是基于权限管理策略下Jenkins的Git远程触发构建流程了。

### 2.10.8 安装必备插件

- 安转`Publish Over SSH`

https://plugins.jenkins.io/publish-over-ssh/

![image-20221104235412138](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20221104235412138.png)

- `Git Parameter`

https://plugins.jenkins.io/git-parameter/

![image-20221104235946206](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20221104235946206.png)

- `NodeJS`

https://plugins.jenkins.io/nodejs/

![image-20221105002506912](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20221105002506912.png)

## 2.11 Docker安装自定义WebApp

#### 2.11.1 安装SpringBoot Application

- `maven`目录层级(`Dockerfile`和`pom.xml`同级)

![image-20201108164157599](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201108164157599.png)

- `Dockerfile`文件示例

```dockerfile
FROM openjdk:latest
MAINTAINER 	weasley "1432689025@qq.com"
EXPOSE 10086
ENV LANG C.UTF-8
ENV LANGUAGE C:cn
ENV LC_ALL C.UTF-8
ENV TZ Asia/Shanghai
VOLUME /tmp
ADD target/registry-service.jar /app.jar
RUN bash -c 'touch /app.jar'
ENTRYPOINT ["java","-jar","/app.jar"]
```

- 构建镜像启动容器

```shell
#!/usr/bin/env bash

#创建容器挂载目录
mkdir -pv /usr/local/{webapp,logs,alphahub/rsa}

#此命令会创建出3个目录:

#springboot应用可执行jar所在目录
#/usr/local/webapp

#日志目录,这里不和jar包放通过一个目录
#/usr/local/logs

#JWT私钥/公钥所在目录
#/usr/local/alphahub/rsa

#创建镜像和容器
cd /usr/local/webapp
#springboot应用的名称
appName='registry-service'
#springboot应用的启动端口
appPort='10086'
#springboot应用的版本
appVersion='latest'
#springboot应用的对应的Dockerfile文件名
dockerfileName='DockerfileRegistry'

echo "application name: $appName"
echo "stop container..."
docker stop $appName
echo "remove old container..."
docker rm -f $appName
echo "remove old image of $appNam"
docker rmi $appName
echo "build image..."
#执行指定的Dockerfile文件，初次依赖镜像的时候会下载相应镜像，创建镜像
docker build -t $appName:$appVersion -f ./$dockerfileName .
echo "run container..."
docker run --name $appName --restart=always \
  -p $appPort:$appPort \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -v /usr/local/logs:/usr/local/logs \
  -v /usr/local/alphahub:/usr/local/alphahub \
  -d $appName:$appVersion

#查看容器是否启动成功
docker ps -a
```

> 提示:
>
> 注意容器里面需要挂在在主机的目录, 比如日志目录, 应用总动态创建的目录, 公钥私钥的挂载目录等

#### 2.11.2 安装前端npm web项目

......

## 2.13 Docker安装Jira Server

#### 2.13.1 [Dokerhub链接](https://hub.docker.com/r/atlassian/jira-software)

- 在`MySQL`内创建数据库：`jiradb`

```mysql
DROP DATABASE IF EXISTS `jiradb`;
CREATE DATABASE jiradb CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
#添加远程登录用户
CREATE USER 'jira'@'%' IDENTIFIED WITH mysql_native_password BY 'jira123!';
GRANT ALL PRIVILEGES ON *.* TO 'jira'@'%';
FLUSH PRIVILEGES;
```

- 拉取镜像，创建容器

```bash
#!/usr/bin/env bash
docker pull atlassian/jira-software

docker volume rm jiraVolume
docker volume create --name jiraVolume

#The maximum memory allocation pool (-Xmx) is required to be at least 786m.
docker stop jira
docker rm -f jira
docker run --name="jira" --restart=always \
  -p 7070:8080 \
  -e JAVA_OPTS="-Xms512m -Xmx768m" \
  -e JVM_SUPPORT_RECOMMENDED_ARGS="-Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Xms256m -Xmx768m" \
  -v jiraVolume:/var/atlassian/application-data/jira \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -d atlassian/jira-software

#重启
docker exec -it jira bash
docker restart jira && clear && docker logs -f jira
#日志
clear && docker logs -f jira
```

- 浏览器访问（换成自己的ip）：

http://192.168.40.132:7070

![image-20210103210416732](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210103210416732.png)

#### 2.13.2 为`Jira`配置`MySQL`数据库

1. 示例`dbconfig .xml`文件

> 合理修改`MySQL`的四大连接信息

```xml
<?xml version="1.0" encoding="UTF-8"?>
<jira-database-config>
    <name>defaultDS</name>
    <delegator-name>default</delegator-name>
    <database-type>mysql8</database-type>
    <jdbc-datasource>
        <url>jdbc:mysql://192.168.40.132:33306/jiradb?serverTimezone=Asia/Shanghai&amp;useSSL=false&amp;allowPublicKeyRetrieval=true&amp;autoReconnect=true&amp;allowMultiQueries=true&amp;sessionVariables=default_storage_engine=InnoDB</url>
        <driver-class>com.mysql.cj.jdbc.Driver</driver-class>
        <username>root</username>
        <password>123456</password>
        <pool-min-size>20</pool-min-size>
        <pool-max-size>20</pool-max-size>
        <pool-max-wait>30000</pool-max-wait>
        <pool-max-idle>20</pool-max-idle>
        <pool-remove-abandoned>true</pool-remove-abandoned>
        <pool-remove-abandoned-timeout>300</pool-remove-abandoned-timeout>

        <validation-query>select 1</validation-query>
        <min-evictable-idle-time-millis>60000</min-evictable-idle-time-millis>
        <time-between-eviction-runs-millis>300000</time-between-eviction-runs-millis>

        <pool-test-while-idle>true</pool-test-while-idle>
        <pool-test-on-borrow>false</pool-test-on-borrow>
        <validation-query-timeout>3</validation-query-timeout>
    </jdbc-datasource>
</jira-database-config>
```

2. 下载`mysql-connector-java.x.y.z.jar`并配置`dbconfig .xml`

- 下载：mysql-connector-java.x.y.z.jar

https://mvnrepository.com/artifact/mysql/mysql-connector-java

https://repo1.maven.org/maven2/mysql/mysql-connector-java/8.0.22/mysql-connector-java-8.0.22.jar

- 准备`jiradb`的`dbconfig .xml`文件

- Tips：修改`MySQL`的四大连接信息：url，driver-class，username，password

```bash
#!/usr/bin/env bash

JIRA_DIR="/usr/local/jira_home"
DB_CONFIG="dbconfig.xml"
DB_CONFIG_BAK="dbconfig_bak.xml"
MYSQL_CONNECTOR="mysql-connector-java-8.0.22.jar"
JIRA_CONTAINER_NAME="jira"
sudo mkdir -pv ${JIRA_DIR}
#备份容器内部jira的dbconfig.xml文件，将容器内的文件复制到宿主机: docker cp 容器id:容器文件位置  需要拷贝到的宿主位置
docker cp ${JIRA_CONTAINER_NAME}:/var/atlassian/application-data/jira/dbconfig.xml ${JIRA_DIR}
sudo mv -fv ${JIRA_DIR}/${DB_CONFIG} ${JIRA_DIR}/${DB_CONFIG_BAK}
#
# 1. 动态创建Jira的数据库配置文件dbconfig.xml
#
sudo tee ${JIRA_DIR}/${DB_CONFIG} <<-'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<jira-database-config>
    <name>defaultDS</name>
    <delegator-name>default</delegator-name>
    <database-type>mysql8</database-type>
    <jdbc-datasource>
        <url>jdbc:mysql://192.168.31.49:3306/jiradb?serverTimezone=Asia/Shanghai&amp;useSSL=false&amp;allowPublicKeyRetrieval=true&amp;autoReconnect=true&amp;allowMultiQueries=true&amp;sessionVariables=default_storage_engine=InnoDB</url>
        <driver-class>com.mysql.cj.jdbc.Driver</driver-class>
        <username>root</username>
        <password>123456</password>

        <pool-min-size>20</pool-min-size>
        <pool-max-size>20</pool-max-size>
        <pool-max-wait>30000</pool-max-wait>
        <pool-max-idle>20</pool-max-idle>
        <pool-remove-abandoned>true</pool-remove-abandoned>
        <pool-remove-abandoned-timeout>300</pool-remove-abandoned-timeout>

        <validation-query>select 1</validation-query>
        <min-evictable-idle-time-millis>60000</min-evictable-idle-time-millis>
        <time-between-eviction-runs-millis>300000</time-between-eviction-runs-millis>

        <pool-test-while-idle>true</pool-test-while-idle>
        <pool-test-on-borrow>false</pool-test-on-borrow>
        <validation-query-timeout>3</validation-query-timeout>
    </jdbc-datasource>
</jira-database-config>
EOF
#
# 2. 操作jira容器使数据库配置文件dbconfig.xml生效
#
# 将[mysql-connector-java.x.y.z.jar]上传到[/usr/local/jira_home]目录下
docker cp ${JIRA_DIR}/${MYSQL_CONNECTOR} ${JIRA_CONTAINER_NAME}:/opt/atlassian/jira/lib

# 修改dbconfig.xml后，复制回jira容器
docker cp ${JIRA_DIR}/${DB_CONFIG} ${JIRA_CONTAINER_NAME}:/var/atlassian/application-data/jira

# 进入Jira容器内部
# docker exec -it jira bash
# chmod -v 777 /var/atlassian

docker restart jira && clear && docker logs -f jira

```

```shell
#查看日志
clear && docker logs -f jira
```

![image-20210103230225313](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210103230225313.png)

创建来了jira数据库：

![image-20210103230406496](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210103230406496.png)

#### 2.13.3 破解Jira

```bash
#下载破解包【atlassian-extras-3.2.jar】
#地址：https://files-cdn.cnblogs.com/files/tchua/atlassian-extras-3.2.rar

#替换破解包
#通过docker ps 获取jira容器id，然后把破解包上传至宿主机通过docker cp 命令复制到容器中，重启jira容器　
cd /usr/local/jira_home
#破解包上传至宿主机通过docker cp命令复制到jira容器中
docker cp atlassian-extras-3.2.jar jira:/opt/atlassian/jira/atlassian-jira/WEB-INF/lib
docker restart jira
#查看日志
clear && docker logs -f jira
```

效果：

![image-20210103235256815](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210103235256815.png)

## 2.14 Docker安装Zipkin链路追踪

#### 2.14.1 mysql脚本

```mysql
CREATE DATABASE IF NOT EXISTS `zipkin` CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_general_ci';

DROP TABLE IF EXISTS `zipkin`.`zipkin_annotations`;
CREATE TABLE `zipkin`.`zipkin_annotations`
(
    `trace_id_high`         bigint                                                  NOT NULL DEFAULT '0' COMMENT 'If non zero, this means the trace uses 128 bit traceIds instead of 64 bit',
    `trace_id`              bigint                                                  NOT NULL COMMENT 'coincides with zipkin_spans.trace_id',
    `span_id`               bigint                                                  NOT NULL COMMENT 'coincides with zipkin_spans.id',
    `a_key`                 varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT 'BinaryAnnotation.key or Annotation.value if type == -1',
    `a_value`               blob COMMENT 'BinaryAnnotation.value(), which must be smaller than 64KB',
    `a_type`                int                                                     NOT NULL COMMENT 'BinaryAnnotation.type() or -1 if Annotation',
    `a_timestamp`           bigint                                                           DEFAULT NULL COMMENT 'Used to implement TTL; Annotation.timestamp or zipkin_spans.timestamp',
    `endpoint_ipv4`         int                                                              DEFAULT NULL COMMENT 'Null when Binary/Annotation.endpoint is null',
    `endpoint_ipv6`         binary(16)                                                       DEFAULT NULL COMMENT 'Null when Binary/Annotation.endpoint is null, or no IPv6 address',
    `endpoint_port`         smallint                                                         DEFAULT NULL COMMENT 'Null when Binary/Annotation.endpoint is null',
    `endpoint_service_name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci          DEFAULT NULL COMMENT 'Null when Binary/Annotation.endpoint is null',
    UNIQUE KEY `trace_id_high` (`trace_id_high`, `trace_id`, `span_id`, `a_key`, `a_timestamp`) USING BTREE COMMENT 'Ignore insert on duplicate',
    KEY `trace_id_high_2` (`trace_id_high`, `trace_id`, `span_id`) USING BTREE COMMENT 'for joining with zipkin_spans',
    KEY `trace_id_high_3` (`trace_id_high`, `trace_id`) USING BTREE COMMENT 'for getTraces/ByIds',
    KEY `endpoint_service_name` (`endpoint_service_name`) USING BTREE COMMENT 'for getTraces and getServiceNames',
    KEY `a_type` (`a_type`) USING BTREE COMMENT 'for getTraces and autocomplete values',
    KEY `a_key` (`a_key`) USING BTREE COMMENT 'for getTraces and autocomplete values',
    KEY `trace_id` (`trace_id`, `span_id`, `a_key`) USING BTREE COMMENT 'for dependencies job'
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb3;

DROP TABLE IF EXISTS `zipkin`.`zipkin_dependencies`;
CREATE TABLE `zipkin`.`zipkin`.`zipkin_dependencies`
(
    `day`         date                                                    NOT NULL,
    `parent`      varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
    `child`       varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
    `call_count`  bigint DEFAULT NULL,
    `error_count` bigint DEFAULT NULL,
    PRIMARY KEY (`day`, `parent`, `child`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb3;

DROP TABLE IF EXISTS `zipkin`.`zipkin_spans`;
CREATE TABLE `zipkin`.`zipkin_spans`
(
    `trace_id_high`       bigint                                                  NOT NULL DEFAULT '0' COMMENT 'If non zero, this means the trace uses 128 bit traceIds instead of 64 bit',
    `trace_id`            bigint                                                  NOT NULL,
    `id`                  bigint                                                  NOT NULL,
    `name`                varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
    `remote_service_name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci          DEFAULT NULL,
    `parent_id`           bigint                                                           DEFAULT NULL,
    `debug`               bit(1)                                                           DEFAULT NULL,
    `start_ts`            bigint                                                           DEFAULT NULL COMMENT 'Span.timestamp(): epoch micros used for endTs query and to implement TTL',
    `duration`            bigint                                                           DEFAULT NULL COMMENT 'Span.duration(): micros used for minDuration and maxDuration query',
    PRIMARY KEY (`trace_id_high`, `trace_id`, `id`) USING BTREE,
    KEY `trace_id_high` (`trace_id_high`, `trace_id`) USING BTREE COMMENT 'for getTracesByIds',
    KEY `name` (`name`) USING BTREE COMMENT 'for getTraces and getSpanNames',
    KEY `remote_service_name` (`remote_service_name`) USING BTREE COMMENT 'for getTraces and getRemoteServiceNames',
    KEY `start_ts` (`start_ts`) USING BTREE COMMENT 'for getTraces ordering and range'
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb3;

```

#### 2.14.2 安装shell脚本

```bash
#!/bin/bash

BASE_DIR="/usr/local/zipkin"
# 浏览器打开链接[https://search.maven.org/remote_content?g=io.zipkin&a=zipkin-server&v=LATEST&c=exec]下载个最新版, 提取版本号；
VERSION="2.23.16"
APP=zipkin-server-${VERSION}-exec
DOCKER_FILE="${BASE_DIR}/Dockerfile"
mkdir -pv ${BASE_DIR}

cd ${BASE_DIR} || exit

rm -rfv ${BASE_DIR}/${APP}.jar
wget https://repo1.maven.org/maven2/io/zipkin/zipkin-server/${VERSION}/zipkin-server-${VERSION}-exec.jar

rm -rfv ${DOCKER_FILE}
{
  echo FROM openjdk:11
  echo RUN mkdir -pv ${BASE_DIR}/
  echo ADD ${APP}.jar ${BASE_DIR}/${APP}.jar
  echo EXPOSE 9411
  echo WORKDIR ${BASE_DIR}/
  echo ENTRYPOINT [\"java\",\"-jar\", \"${BASE_DIR}/${APP}.jar\"]
} >>${DOCKER_FILE} &&
  clear && cat ${DOCKER_FILE}

rm -rfv ${APP}.jar

docker stop zipkin && docker rm -f zipkin
docker rmi -f zipkin:latest
docker build -f ${DOCKER_FILE} -t zipkin:latest .

docker stop zipkin && docker rm -f zipkin
#以下mysql的参数需要修改
docker run --name zipkin --restart=always \
  -p 9411:9411 \
  -e JAVA_OPTS="-Xms512m -Xmx512m" \
  -e STORAGE_TYPE="mysql" \
  -e MYSQL_HOST="192.168.31.105" \
  -e MYSQL_TCP_PORT="3306" \
  -e MYSQL_DB="zipkin" \
  -e MYSQL_USER="root" \
  -e MYSQL_PASS="123456" \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -d zipkin:latest

clear && docker logs -f zipkin
```

访问：http://你的ip:9411

![image-20210104223844512](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210104223844512.png)

![image-20210104224448712](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210104224448712.png)

## 2.15 Docker安装maven私服

#### 2.15.1 搭建jfrog artifactory OSS开源版

[官方链接](https://jfrog.com/open-source/#artifactory)

```bash
docker pull docker.bintray.io/jfrog/artifactory-oss:latest
```

[官方安装链接](https://www.jfrog.com/confluence/display/JFROG/Installing+Artifactory)

#### 2.15.2 Running the Artifactory OSS Container

```shell
#!/usr/bin/env bash

JFROG_HOME="/usr/local/jfrog_hmoe"
#sudo rm -rfv $JFROG_HOME
sudo mkdir -pv $JFROG_HOME

mkdir -p $JFROG_HOME/artifactory/var/etc/
cd $JFROG_HOME/artifactory/var/etc/
touch ./system.yaml
chown -R $UID:$GID $JFROG_HOME/artifactory/var
chmod -R 777 $JFROG_HOME/artifactory/var

docker stop artifactory
docker rm -f artifactory

docker run --name="artifactory" \
  --restart=always \
  -p 9091:8081 \
  -p 9092:8082 \
  -e JAVA_OPTS="-Xms512m -Xmx512m" \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -v $JFROG_HOME/artifactory/var/:/var/opt/jfrog/artifactory \
  -d docker.bintray.io/jfrog/artifactory-oss:latest

#artifactory logs
clear && docker logs -f artifactory
#新开一个SSH窗口进入容器
docker exec -it artifactory bash

```

#### 2.15.3 Accessing Artifactory

Access Artifactory from your browser at: `http://SERVER_HOSTNAME:8082/ui/`*.*

For example, on your local machine: `http://192.168.40.132:9092/ui/`

Username: admin

Password: password

![image-20210119013057840](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210119013057840.png)

*
*![image-20210119013144709](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210119013144709.png)
**

- 改密码: Password123456

![image-20210119013418701](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210119013418701.png)

## 2.16 docker安装PostgresSQL

- 安装`PostgresSQL`，若`docker`镜像不存在会自动拉取

```shell
#!/bin/bash

#安装目录
BASE_DIR_POSTGRES="/usr/local/postgres"
#主机ip，填自己虚拟主机的ip
HOST="192.168.31.23"

clear && mkdir -pv ${BASE_DIR_POSTGRES}

docker stop postgres && docker rm -f postgres
docker rmi postgres && docker pull postgres
docker run --name postgres --restart=always \
  --net mynet \
  -p 5432:5432 \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -v ${BASE_DIR_POSTGRES}:/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=123456 \
  -e PGDATA=/var/lib/postgresql/data/pgdata \
  -d postgres

#日志
# clear && docker logs -f postgres

docker exec -it postgres psql -h ${HOST} -U postgres

# 进入容器界面运行下面的SQL, 创建数据库，如：创建sonarqube的数据库
CREATE ROLE "root" SUPERUSER CREATEDB CREATEROLE LOGIN REPLICATION BYPASSRLS PASSWORD '123456';
CREATE DATABASE "sonarqube" WITH OWNER = "root" TEMPLATE = "template0" ENCODING = 'UTF8' TABLESPACE = "pg_default";
COMMENT ON DATABASE "sonarqube" IS 'sonarqube数据库';

#退出postgressql界面, 退出容器。
```

> 几个数据库参数：
>
> 用户名: root
>
> 密码: 123456

- 开启`PostgresSQL`远程访问

修改文件`pg_hba.conf`,创建容器时已经挂载到宿主机目录: /usr/local/postgres/pgdata/pg_hba.conf, 在文件末尾添加一行

```shell
echo 'host all all 0.0.0.0/0 md5' >>/usr/local/postgres/pgdata/pg_hba.conf

#重启容器
docker restart postgres
#日志
clear && docker logs -f postgres
```

- 使用navicat连接验证下

![image-20210626231545007](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210626231545007.png)

## 2.17 docker安装SonarQube

*注意： SonarQube高版本已经不支持MySQL数据库持久化了，本教程使用PostgresSQL*

```sql
# 事先去数据库创建sonar的数据
CREATE ROLE "root" SUPERUSER CREATEDB CREATEROLE LOGIN REPLICATION BYPASSRLS PASSWORD '123456';
CREATE DATABASE "sonarqube" WITH OWNER = "root" TEMPLATE = "template0" ENCODING = 'UTF8' TABLESPACE = "pg_default";
COMMENT ON DATABASE "sonarqube" IS 'sonarqube数据库';
```

```shell
#!/bin/bash

#sonarqube的宿主机挂载目录
BASE_DIR_SONARQUBE="/usr/local/sonarqube"
#此主机ip是你虚拟主机的POSTGRESQL的ip
POSTGRESQL_HOST="192.168.31.23"

clear && rm -rfv ${BASE_DIR_SONARQUBE}
mkdir -pv ${BASE_DIR_SONARQUBE}/{data,extensions,logs}
chown -vR 0999 ${BASE_DIR_SONARQUBE}/

docker stop sonarqube && docker rm -f sonarqube
docker rmi sonarqube && docker pull sonarqube

docker network create --driver bridge --subnet 172.18.0.0/16 --gateway 172.18.0.1 mynet
# 社区版本（本示例采用），9001是宿主机的端口用作web访问
docker stop sonarqube && docker rm -f sonarqube
docker run --name sonarqube --restart=always \
  -p 9001:9000 \
  -p 9092:9092 \
  --net mynet \
  -e JAVA_OPTS="-Xmx512m -Xms512m" \
  -e SONAR_JDBC_USERNAME=root \
  -e SONAR_JDBC_PASSWORD=123456 \
  -e SONAR_JDBC_URL="jdbc:postgresql://postgres:5432/sonarqube" \
  -v ${BASE_DIR_SONARQUBE}/data:/opt/sonarqube/data \
  -v ${BASE_DIR_SONARQUBE}/extensions:/opt/sonarqube/extensions \
  -v ${BASE_DIR_SONARQUBE}/logs:/opt/sonarqube/logs \
  -d sonarqube

# 企业版本
#docker run -d -p 9001:9000  -p 9092:9092 --name sonarqube --restart=always \
#-v ${BASE_DIR_SONARQUBE}/data:/opt/sonarqube/data \
#-v ${BASE_DIR_SONARQUBE}/extensions:/opt/sonarqube/extensions \
#-v ${BASE_DIR_SONARQUBE}/logs:/opt/sonarqube/logs \
#-e SONAR_JDBC_USERNAME=root \
#-e SONAR_JDBC_PASSWORD=123456 \
#-e SONAR_JDBC_URL="jdbc:postgresql://${POSTGRESQL_HOST}:5432/sonarqube" \
#sonarqube:enterprise

#编辑此文件, 再末尾追加(已经有就不用追加): vm.max_map_count=655360
vim /etc/sysctl.conf
sysctl -p

# echo后边用单引号包围要添加的内容
echo 'vm.max_map_count=655360' >>/etc/sysctl.conf
sysctl -p

docker restart sonarqube
#查看容器日志
clear && docker logs -f sonarqube
```

- 设置中文语言

浏览器访问sonarqube web界面: http://192.168.40.132:9001

默认用户名密码为：admin/admin

进入界面后: `Administration` --> `marketplace` --> `搜索chinese`，点击同一条款即可安装，安装完根据提示重启web界面语言便是中文。

到此你的SonarQube安装好了：

![image-20210626233936489](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210626233936489.png)

## 2.18 docker安装torna

[torna简介](https://gitee.com/durcframework/torna)

### 初始化MySQL数据库

我这里采用的是MySQL_8.0.25

```mysql
-- 适用于MySQL5.6.5+
-- 备注：5.5.3开始支持utf8mb4，5.6.5开始支持CURRENT_TIMESTAMP(datetime)
-- 干净的数据

CREATE DATABASE IF NOT EXISTS `torna` DEFAULT CHARACTER SET utf8mb4 DEFAULT COLLATE utf8mb4_general_ci;
USE `torna`;


DROP TABLE IF EXISTS `user_subscribe`;
DROP TABLE IF EXISTS `user_message`;
DROP TABLE IF EXISTS `user_info`;
DROP TABLE IF EXISTS `user_dingtalk_info`;
DROP TABLE IF EXISTS `system_config`;
DROP TABLE IF EXISTS `space_user`;
DROP TABLE IF EXISTS `space`;
DROP TABLE IF EXISTS `share_content`;
DROP TABLE IF EXISTS `share_config`;
DROP TABLE IF EXISTS `prop`;
DROP TABLE IF EXISTS `project_user`;
DROP TABLE IF EXISTS `project`;
DROP TABLE IF EXISTS `open_user`;
DROP TABLE IF EXISTS `module_config`;
DROP TABLE IF EXISTS `module`;
DROP TABLE IF EXISTS `mock_config`;
DROP TABLE IF EXISTS `enum_item`;
DROP TABLE IF EXISTS `enum_info`;
DROP TABLE IF EXISTS `doc_param`;
DROP TABLE IF EXISTS `doc_info`;
DROP TABLE IF EXISTS `compose_project`;
DROP TABLE IF EXISTS `compose_doc`;


CREATE TABLE `compose_doc`
(
    `id`           bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `doc_id`       bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'doc_info.id',
    `project_id`   bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'compose_project.id',
    `is_folder`    tinyint(4)          NOT NULL DEFAULT '0' COMMENT '是否文件夹',
    `folder_name`  varchar(64)         NOT NULL DEFAULT '' COMMENT '文件夹名称',
    `parent_id`    bigint(20)          NOT NULL DEFAULT '0',
    `origin`       varchar(128)        NOT NULL DEFAULT '' COMMENT '文档来源',
    `is_deleted`   tinyint(4)          NOT NULL DEFAULT '0',
    `creator`      varchar(64)         NOT NULL DEFAULT '' COMMENT '创建人',
    `order_index`  int(10) unsigned    NOT NULL DEFAULT '0',
    `gmt_create`   datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified` datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    KEY `idx_projectid` (`project_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='文档引用';


CREATE TABLE `compose_project`
(
    `id`            bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `name`          varchar(64)         NOT NULL DEFAULT '' COMMENT '项目名称',
    `description`   varchar(128)        NOT NULL DEFAULT '' COMMENT '项目描述',
    `space_id`      bigint(20)          NOT NULL DEFAULT '0' COMMENT '所属空间，space.id',
    `type`          tinyint(4)          NOT NULL DEFAULT '1' COMMENT '访问形式，1：公开，2：加密',
    `password`      varchar(64)         NOT NULL DEFAULT '' COMMENT '访问密码',
    `creator_id`    bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '创建者userid',
    `creator_name`  varchar(64)         NOT NULL DEFAULT '',
    `modifier_id`   bigint(20) unsigned NOT NULL DEFAULT '0',
    `modifier_name` varchar(64)         NOT NULL DEFAULT '',
    `order_index`   int(11)             NOT NULL DEFAULT '0' COMMENT '排序索引',
    `status`        tinyint(4)          NOT NULL DEFAULT '1' COMMENT '1：有效，0：无效',
    `is_deleted`    tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`    datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified`  datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    KEY `idx_spaceid` (`space_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='组合项目表';


CREATE TABLE `doc_info`
(
    `id`                    bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `data_id`               varchar(64)         NOT NULL DEFAULT '' COMMENT '唯一id，接口规则：md5(module_id:parent_id:url:http_method)。分类规则：md5(module_id:parent_id:name)',
    `name`                  varchar(128)        NOT NULL DEFAULT '' COMMENT '文档名称',
    `description`           text                NOT NULL COMMENT '文档描述',
    `author`                varchar(64)         NOT NULL DEFAULT '' COMMENT '维护人',
    `type`                  tinyint(4)          NOT NULL DEFAULT '0' COMMENT '0:http,1:dubbo',
    `url`                   varchar(128)        NOT NULL DEFAULT '' COMMENT '访问URL',
    `http_method`           varchar(12)         NOT NULL DEFAULT '' COMMENT 'http方法',
    `content_type`          varchar(128)        NOT NULL DEFAULT '' COMMENT 'contentType',
    `is_folder`             tinyint(4)          NOT NULL DEFAULT '0' COMMENT '是否是分类，0：不是，1：是',
    `parent_id`             bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '父节点',
    `module_id`             bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '模块id，module.id',
    `is_use_global_headers` tinyint(4)          NOT NULL DEFAULT '1' COMMENT '是否使用全局请求参数',
    `is_use_global_params`  tinyint(4)          NOT NULL DEFAULT '1' COMMENT '是否使用全局请求参数',
    `is_use_global_returns` tinyint(4)          NOT NULL DEFAULT '1' COMMENT '是否使用全局返回参数',
    `is_request_array`      tinyint(4)          NOT NULL DEFAULT '0' COMMENT '是否请求数组',
    `is_response_array`     tinyint(4)          NOT NULL DEFAULT '0' COMMENT '是否返回数组',
    `request_array_type`    varchar(16)         NOT NULL DEFAULT 'object' COMMENT '请求数组时元素类型',
    `response_array_type`   varchar(16)         NOT NULL DEFAULT 'object' COMMENT '返回数组时元素类型',
    `create_mode`           tinyint(4)          NOT NULL DEFAULT '0' COMMENT '新增操作方式，0：人工操作，1：开放平台推送',
    `modify_mode`           tinyint(4)          NOT NULL DEFAULT '0' COMMENT '修改操作方式，0：人工操作，1：开放平台推送',
    `creator_id`            bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '创建人',
    `creator_name`          varchar(64)         NOT NULL DEFAULT '' COMMENT '创建者昵称user_info.nickname',
    `modifier_id`           bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '修改人',
    `modifier_name`         varchar(64)         NOT NULL DEFAULT '' COMMENT '创建者昵称user_info.realname',
    `order_index`           int(11)             NOT NULL DEFAULT '0' COMMENT '排序索引',
    `remark`                varchar(128)        NOT NULL DEFAULT '' COMMENT '备注',
    `is_show`               tinyint(4)          NOT NULL DEFAULT '1' COMMENT '是否显示',
    `is_deleted`            tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`            datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified`          datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    UNIQUE KEY `uk_data_id` (`data_id`) USING BTREE,
    KEY `idx_moduleid` (`module_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='文档信息';


CREATE TABLE `doc_param`
(
    `id`            bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `data_id`       varchar(64)         NOT NULL DEFAULT '' COMMENT '唯一id，md5(doc_id:parent_id:style:name)',
    `name`          varchar(64)         NOT NULL DEFAULT '' COMMENT '字段名称',
    `type`          varchar(64)         NOT NULL DEFAULT 'String' COMMENT '字段类型',
    `required`      tinyint(4)          NOT NULL DEFAULT '0' COMMENT '是否必须，1：是，0：否',
    `max_length`    varchar(64)         NOT NULL DEFAULT '-' COMMENT '最大长度',
    `example`       varchar(1024)       NOT NULL DEFAULT '' COMMENT '示例值',
    `description`   text                NOT NULL COMMENT '描述',
    `enum_id`       bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'enum_info.id',
    `doc_id`        bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'doc_info.id',
    `parent_id`     bigint(20) unsigned NOT NULL DEFAULT '0',
    `style`         tinyint(4)          NOT NULL DEFAULT '0' COMMENT '0：path, 1：header， 2：请求参数，3：返回参数，4：错误码',
    `create_mode`   tinyint(4)          NOT NULL DEFAULT '0' COMMENT '新增操作方式，0：人工操作，1：开放平台推送',
    `modify_mode`   tinyint(4)          NOT NULL DEFAULT '0' COMMENT '修改操作方式，0：人工操作，1：开放平台推送',
    `creator_id`    bigint(20) unsigned NOT NULL DEFAULT '0',
    `creator_name`  varchar(64)         NOT NULL DEFAULT '',
    `modifier_id`   bigint(20) unsigned NOT NULL DEFAULT '0',
    `modifier_name` varchar(64)         NOT NULL DEFAULT '',
    `order_index`   int(11)             NOT NULL DEFAULT '0' COMMENT '排序索引',
    `is_deleted`    tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`    datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified`  datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    UNIQUE KEY `uk_dataid` (`data_id`) USING BTREE,
    KEY `idx_docid` (`doc_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='文档参数';


CREATE TABLE `enum_info`
(
    `id`           bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `data_id`      varchar(64)         NOT NULL DEFAULT '' COMMENT '唯一id，md5(module_id:name)',
    `name`         varchar(128)        NOT NULL DEFAULT '' COMMENT '枚举名称',
    `description`  varchar(128)        NOT NULL DEFAULT '' COMMENT '枚举说明',
    `module_id`    bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'module.id',
    `is_deleted`   tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`   datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified` datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    KEY `idx_moduleid` (`module_id`) USING BTREE,
    KEY `idx_dataid` (`data_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='枚举信息';


CREATE TABLE `enum_item`
(
    `id`           bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `enum_id`      bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'enum_info.id',
    `name`         varchar(128)        NOT NULL DEFAULT '' COMMENT '名称，字面值',
    `type`         varchar(64)         NOT NULL DEFAULT '' COMMENT '类型',
    `value`        varchar(64)         NOT NULL DEFAULT '' COMMENT '枚举值',
    `description`  varchar(128)        NOT NULL DEFAULT '' COMMENT '枚举描述',
    `order_index`  int(11)             NOT NULL DEFAULT '0' COMMENT '排序索引',
    `is_deleted`   tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`   datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified` datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    KEY `idx_enumid` (`enum_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='枚举详情';


CREATE TABLE `mock_config`
(
    `id`                bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `name`              varchar(64)         NOT NULL DEFAULT '' COMMENT '名称',
    `data_id`           varchar(64)         NOT NULL DEFAULT '' COMMENT 'md5(path+query+body)',
    `path`              varchar(128)        NOT NULL DEFAULT '',
    `ip`                varchar(64)         NOT NULL DEFAULT '' COMMENT '过滤ip',
    `request_data`      text                NOT NULL COMMENT '请求参数',
    `request_data_type` tinyint(4)          NOT NULL DEFAULT '0' COMMENT '参数类型，0：KV形式，1：json形式',
    `http_status`       int(11)             NOT NULL DEFAULT '200' COMMENT 'http状态',
    `delay_mills`       int(11)             NOT NULL DEFAULT '0' COMMENT '延迟时间，单位毫秒',
    `result_type`       tinyint(4)          NOT NULL DEFAULT '0' COMMENT '返回类型，0：自定义内容，1：脚本内容',
    `response_headers`  text                NOT NULL COMMENT '响应header，数组结构',
    `response_body`     text                NOT NULL COMMENT '响应结果',
    `mock_script`       text COMMENT 'mock脚本',
    `mock_result`       text COMMENT 'mock结果',
    `doc_id`            bigint(20)          NOT NULL DEFAULT '0' COMMENT '文档id',
    `remark`            varchar(128)        NOT NULL DEFAULT '' COMMENT '备注',
    `creator_id`        bigint(20)          NOT NULL DEFAULT '0' COMMENT '创建人id',
    `creator_name`      varchar(64)         NOT NULL DEFAULT '' COMMENT '创建人姓名',
    `modifier_id`       bigint(20)          NOT NULL DEFAULT '0' COMMENT '修改人id',
    `modifier_name`     varchar(64)                  DEFAULT NULL COMMENT '修改人',
    `is_deleted`        tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`        datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified`      datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    KEY `idx_docid` (`doc_id`) USING BTREE,
    KEY `idx_dataid` (`data_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='mock配置';


CREATE TABLE `module`
(
    `id`                  bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `name`                varchar(64)         NOT NULL DEFAULT '' COMMENT '模块名称',
    `project_id`          bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'project.id',
    `type`                tinyint(4)          NOT NULL DEFAULT '0' COMMENT '模块类型，0：自定义添加，1：swagger导入，2：postman导入',
    `import_url`          varchar(128)        NOT NULL DEFAULT '' COMMENT '导入url',
    `basic_auth_username` varchar(128)        NOT NULL DEFAULT '' COMMENT 'basic认证用户名',
    `basic_auth_password` varchar(128)        NOT NULL DEFAULT '' COMMENT 'basic认证密码',
    `token`               varchar(128)        NOT NULL DEFAULT '' COMMENT '开放接口调用token',
    `create_mode`         tinyint(4)          NOT NULL DEFAULT '0' COMMENT '新增操作方式，0：人工操作，1：开放平台推送',
    `modify_mode`         tinyint(4)          NOT NULL DEFAULT '0' COMMENT '修改操作方式，0：人工操作，1：开放平台推送',
    `creator_id`          bigint(20) unsigned NOT NULL DEFAULT '0',
    `modifier_id`         bigint(20) unsigned NOT NULL DEFAULT '0',
    `order_index`         int(11)             NOT NULL DEFAULT '0' COMMENT '排序索引',
    `is_deleted`          tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`          datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified`        datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    UNIQUE KEY `uk_token` (`token`) USING BTREE,
    KEY `idx_projectid` (`project_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='项目模块';


CREATE TABLE `module_config`
(
    `id`           bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `module_id`    bigint(11) unsigned NOT NULL DEFAULT '0',
    `type`         tinyint(4)          NOT NULL DEFAULT '0' COMMENT '配置类型，1：全局header',
    `config_key`   varchar(128)        NOT NULL DEFAULT '' COMMENT '配置key',
    `config_value` varchar(128)        NOT NULL DEFAULT '' COMMENT '配置值',
    `extend_id`    bigint(20) unsigned NOT NULL DEFAULT '0',
    `description`  varchar(256)        NOT NULL DEFAULT '' COMMENT '描述',
    `is_deleted`   tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`   datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified` datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    KEY `idx_moduleid_type` (`module_id`, `type`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='系统配置';


CREATE TABLE `open_user`
(
    `id`           bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `app_key`      varchar(100)        NOT NULL COMMENT 'appKey',
    `secret`       varchar(200)                 DEFAULT NULL COMMENT 'secret',
    `status`       tinyint(4) unsigned NOT NULL DEFAULT '1' COMMENT '1启用，0禁用',
    `applicant`    varchar(64)         NOT NULL DEFAULT '',
    `space_id`     bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'space.id',
    `is_deleted`   tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`   datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified` datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    UNIQUE KEY `uk_app_key` (`app_key`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='开放用户';


CREATE TABLE `project`
(
    `id`            bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `name`          varchar(64)         NOT NULL DEFAULT '' COMMENT '项目名称',
    `description`   varchar(128)        NOT NULL DEFAULT '' COMMENT '项目描述',
    `space_id`      bigint(20)          NOT NULL DEFAULT '0' COMMENT '所属空间，space.id',
    `is_private`    tinyint(4)          NOT NULL DEFAULT '0' COMMENT '是否私有项目，1：是，0：否',
    `creator_id`    bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '创建者userid',
    `creator_name`  varchar(64)         NOT NULL DEFAULT '',
    `modifier_id`   bigint(20) unsigned NOT NULL DEFAULT '0',
    `modifier_name` varchar(64)         NOT NULL DEFAULT '',
    `order_index`   int(11)             NOT NULL DEFAULT '0' COMMENT '排序索引',
    `is_deleted`    tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`    datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified`  datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    KEY `uk_name` (`creator_id`, `name`) USING BTREE,
    KEY `idx_spaceid` (`space_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='项目表';


CREATE TABLE `project_user`
(
    `id`           bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `project_id`   bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'project.id',
    `user_id`      bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'user_info.id',
    `role_code`    varchar(64)         NOT NULL DEFAULT '0' COMMENT '角色，guest：访客，dev：开发者，admin：项目管理员',
    `is_deleted`   tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`   datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified` datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    UNIQUE KEY `uk_projectid_userid` (`project_id`, `user_id`) USING BTREE,
    KEY `idx_userid` (`user_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='项目用户关系表';


CREATE TABLE `prop`
(
    `id`           bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `ref_id`       bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '关联id',
    `type`         tinyint(4)          NOT NULL DEFAULT '0' COMMENT '类型，0：doc_info属性',
    `name`         varchar(64)         NOT NULL DEFAULT '',
    `val`          text                NOT NULL,
    `gmt_create`   datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified` datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    UNIQUE KEY `uk_docid_name` (`ref_id`, `type`, `name`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='属性表';


CREATE TABLE `share_config`
(
    `id`           bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `type`         tinyint(4)          NOT NULL DEFAULT '0' COMMENT '分享形式，1：公开，2：加密',
    `password`     varchar(128)        NOT NULL DEFAULT '' COMMENT '密码',
    `status`       tinyint(4)          NOT NULL DEFAULT '1' COMMENT '状态，1：有效，0：无效',
    `module_id`    bigint(20)          NOT NULL DEFAULT '0' COMMENT 'module.id',
    `is_all`       tinyint(4)          NOT NULL DEFAULT '0' COMMENT '是否为全部文档',
    `is_deleted`   tinyint(4)          NOT NULL DEFAULT '0',
    `remark`       varchar(128)        NOT NULL DEFAULT '' COMMENT '备注',
    `creator_name` varchar(64)         NOT NULL DEFAULT '' COMMENT '创建人',
    `gmt_create`   datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified` datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    KEY `idx_moduleid` (`module_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='分享配置表';


CREATE TABLE `share_content`
(
    `id`              bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `share_config_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'share_config.id',
    `doc_id`          bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '文档id',
    `parent_id`       bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '父id',
    `is_share_folder` tinyint(4)          NOT NULL DEFAULT '0' COMMENT '是否分享整个分类',
    `is_deleted`      tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`      datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified`    datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    KEY `idx_shareconfigid_docid` (`share_config_id`, `doc_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='分享详情';


CREATE TABLE `space`
(
    `id`            bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `name`          varchar(64)         NOT NULL DEFAULT '' COMMENT '空间名称',
    `creator_id`    bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '创建者userid',
    `creator_name`  varchar(64)         NOT NULL DEFAULT '',
    `modifier_id`   bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '创建者userid',
    `modifier_name` varchar(64)         NOT NULL DEFAULT '',
    `is_compose`    tinyint(4)          NOT NULL DEFAULT '0' COMMENT '是否组合空间',
    `is_deleted`    tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`    datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified`  datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`)
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='分组表';


CREATE TABLE `space_user`
(
    `id`           bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `user_id`      bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'user_info.id',
    `space_id`     bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'space.id',
    `role_code`    varchar(64)         NOT NULL DEFAULT '' COMMENT '角色，guest：访客，dev：开发者，admin：空间管理员',
    `is_deleted`   tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`   datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified` datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    UNIQUE KEY `uk_groupid_userid` (`space_id`, `user_id`) USING BTREE,
    KEY `idx_userid` (`user_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='分组用户关系表';


CREATE TABLE `system_config`
(
    `id`           bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `config_key`   varchar(64)         NOT NULL DEFAULT '',
    `config_value` varchar(256)        NOT NULL DEFAULT '',
    `remark`       varchar(128)        NOT NULL DEFAULT '',
    `is_deleted`   tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`   datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified` datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    UNIQUE KEY `uk_configkey` (`config_key`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='系统配置表';


CREATE TABLE `user_dingtalk_info`
(
    `id`           bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `nick`         varchar(64)         NOT NULL DEFAULT '' COMMENT '用户在钉钉上面的昵称',
    `name`         varchar(64)         NOT NULL DEFAULT '' COMMENT '员工名称。',
    `email`        varchar(128)        NOT NULL DEFAULT '' COMMENT '员工邮箱。',
    `userid`       varchar(128)        NOT NULL DEFAULT '' COMMENT '员工的userid。',
    `unionid`      varchar(128)        NOT NULL DEFAULT '' COMMENT '用户在当前开放应用所属企业的唯一标识。',
    `openid`       varchar(128)        NOT NULL DEFAULT '' COMMENT '用户在当前开放应用内的唯一标识。',
    `user_info_id` bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'user_info.id',
    `gmt_create`   datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified` datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    UNIQUE KEY `uk_unionid` (`unionid`) USING BTREE,
    KEY `idx_openid` (`openid`) USING BTREE,
    KEY `idx_userid` (`user_info_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='钉钉开放平台用户';


CREATE TABLE `user_info`
(
    `id`             bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `username`       varchar(128)        NOT NULL DEFAULT '' COMMENT '登录账号/邮箱',
    `password`       varchar(128)        NOT NULL DEFAULT '' COMMENT '登录密码',
    `nickname`       varchar(64)         NOT NULL DEFAULT '' COMMENT '昵称',
    `is_super_admin` tinyint(4)          NOT NULL DEFAULT '0' COMMENT '是否是超级管理员',
    `source`         varchar(64)         NOT NULL DEFAULT 'register',
    `email`          varchar(128)        NOT NULL DEFAULT '',
    `status`         tinyint(4)          NOT NULL DEFAULT '1' COMMENT '0：禁用，1：启用，2：重设密码',
    `is_deleted`     tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`     datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified`   datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    UNIQUE KEY `uk_username` (`username`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='用户表';


CREATE TABLE `user_message`
(
    `id`           bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `user_id`      bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'user_info.id',
    `message`      varchar(256)        NOT NULL DEFAULT '',
    `is_read`      tinyint(4)          NOT NULL DEFAULT '0',
    `type`         tinyint(4)          NOT NULL DEFAULT '0' COMMENT '同user_subscribe.type',
    `source_id`    bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT '同user_subscribe.source_id',
    `gmt_create`   datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified` datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    KEY `idx_userid` (`user_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='站内消息';


CREATE TABLE `user_subscribe`
(
    `id`           bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `user_id`      bigint(20) unsigned NOT NULL DEFAULT '0' COMMENT 'user_info.id',
    `type`         tinyint(4)          NOT NULL DEFAULT '0' COMMENT '类型，1：订阅接口，2：订阅项目',
    `source_id`    bigint(20)          NOT NULL DEFAULT '0' COMMENT '关联id，当type=1对应doc_info.id；type=2对应project.id',
    `is_deleted`   tinyint(4)          NOT NULL DEFAULT '0',
    `gmt_create`   datetime                     DEFAULT CURRENT_TIMESTAMP,
    `gmt_modified` datetime                     DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`),
    UNIQUE KEY `uk_userid_type_sourceid` (`user_id`, `type`, `source_id`) USING BTREE
) ENGINE = InnoDB
  DEFAULT CHARSET = utf8mb4 COMMENT ='用户订阅表';



INSERT INTO `system_config` (`id`, `config_key`, `config_value`, `remark`, `is_deleted`, `gmt_create`, `gmt_modified`)
VALUES (1, 'torna.version', '11', '当前内部版本号。不要删除这条记录！！', 0, '2021-05-25 18:03:08', '2021-05-25 18:03:08');

INSERT INTO `user_info` (`id`, `username`, `password`, `nickname`, `is_super_admin`, `source`, `email`, `status`,
                         `is_deleted`, `gmt_create`, `gmt_modified`)
VALUES (1, 'admin@torna.cn', 'f1e27f8ec06b0ea415583c26457dd111', '超级管理员', 1, 'register', 'admin@torna.cn', 1, 0,
        '2020-12-15 09:04:13', '2021-05-25 18:03:07');
```

### 准备docker安装

```shell
#!/bin/bash

CONTAINER_NAME="torna"
IMAGE_NAME="tanghc2020/torna"
BASE_DIR="/usr/local/torna"
CONFIG_FILE="application.properties"

docker stop ${CONTAINER_NAME} && docker rm -f ${CONTAINER_NAME}
docker rmi ${IMAGE_NAME}:latest && docker pull ${IMAGE_NAME}:latest

mkdir -pv ${BASE_DIR}/{config,data}

#运行个临时容器,把application.properties配置文件copy到宿主机
docker run --name torna -p 7700:7700 -d ${IMAGE_NAME}:latest
docker cp ${CONTAINER_NAME}:/torna/config ${BASE_DIR}/config
ls ${BASE_DIR}/config

# 存在application.properties就用vim编辑下就好了, 没有application.properties配置文件, 你手动就创建创建配置文件
rm -rfv ${BASE_DIR}/config/${CONFIG_FILE}
sudo tee ${BASE_DIR}/config/${CONFIG_FILE} <<-'EOF'
# Server port
server.port=7700

# MySQL host, 这里改成你的MySQL的主机ip:端口
mysql.host=192.168.31.105:3306
# Schema name, 数据库名
mysql.schema=torna

# Insure the account can run CREATE/ALTER sql.
mysql.username=root
mysql.password=123456
EOF

#修改权限
chmod -vR 777 ${BASE_DIR}/

#查看配置文件
cat ${BASE_DIR}/config/application.properties

#删除容器
docker stop ${CONTAINER_NAME} && docker rm -f ${CONTAINER_NAME}

#正式安装torna, jvm参数根据自己的机器性能调整, mysql的参数更具自己配置修改
#以创建容器的环境变量里面已经指定mysql的链接参数优先级将会高于application.properties的
docker run --name torna --restart=always \
  -p 7700:7700 \
  -e JAVA_OPTS="-Xms256m -Xmx256m" \
  -e MYSQL_HOST="192.168.31.105:3306" \
  -e MYSQL_SCHEMA="torna" \
  -e MYSQL_USERNAME="root" \
  -e MYSQL_PASSWORD="123456" \
  -v ${BASE_DIR}/config:/torna/config \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -d ${IMAGE_NAME}:latest

#torna日志
clear && docker logs -f torna

```

### Web访问Torna

打开浏览器访问: http://192.168.40.132:7700

超级管理员：admin@torna.cn
密码均为：123456

研发一部空间管理员：dev1admin@torna.cn
研发一部-商城项目（公开）-项目管理员：dev1shop_admin@torna.cn
研发一部-商城项目（公开）-开发者张三：dev1shop_zhangsan@torna.cn
研发一部-访客王五：dev1guest_wangwu@torna.cn

研发二部空间管理员：dev2admin@torna.cn
研发二部-后台项目（私有）-项目管理员：dev2back_admin@torna.cn
研发二部-后台项目（私有）-开发者李四：dev2back_lisi@torna.cn
研发二部-后台项目（私有）-访客：dev2back_guest@torna.cn
研发二部-访客赵六：dev2guest_zhaoliu@torna.cn

![image-20210711171342563](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210711171342563.png)

## 2.19 Docker安装Nacos

提示：安装`nacos`前先配置好`mysql`，脚本如下：

```shell
#!/bin/bash

CONTAINER_NAME="nacos"
IMAGE_NAME="nacos/nacos-server"
BASE_DIR="/usr/local"
WORK_DIR="${BASE_DIR}/${CONTAINER_NAME}"

rm -rfv ${WORK_DIR} && mkdir -pv ${WORK_DIR} && ll ${WORK_DIR}

#准备挂载文件
docker stop nacos && docker rm -f nacos
docker run --name nacos -p 8848:8848 \
  --net mynet \
  -e MODE="standalone" \
  -e JVM_XMS="512m" \
  -e JVM_XMX="512m" \
  -d ${IMAGE_NAME}

docker cp ${CONTAINER_NAME}:/home/nacos/conf ${WORK_DIR}/
docker cp ${CONTAINER_NAME}:/home/nacos/logs ${WORK_DIR}/
ll ${WORK_DIR}/conf

docker network rm mynet
docker network create --driver bridge --subnet 172.18.0.0/16 --gateway 172.18.0.1 mynet

docker stop nacos && docker rm -f nacos
docker run --name nacos --restart=always \
  --net mynet \
  -p 8848:8848 \
  -p 9848:9848 \
  -e MODE="standalone" \
  -e JVM_XMS="512m" \
  -e JVM_XMX="512m" \
  -e SPRING_DATASOURCE_PLATFORM="mysql" \
  -e MYSQL_SERVICE_HOST="mysql" \
  -e MYSQL_SERVICE_PORT="3306" \
  -e MYSQL_SERVICE_DB_NAME="nacos" \
  -e MYSQL_SERVICE_USER="root" \
  -e MYSQL_SERVICE_PASSWORD="123456" \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -v ${WORK_DIR}/conf:/home/nacos/conf \
  -v ${WORK_DIR}/logs:/home/nacos/logs \
  -d ${IMAGE_NAME}

#日志
clear && docker logs -f nacos

```

> `-e MYSQL_SERVICE_HOST="mysql"`表示：`nacos`和`mysql`共同使用一个网络`mynet`
> ，如果不在同一个网络环境，写成ip: `  -e MYSQL_SERVICE_HOST="192.168.31.23"`

## 2.20 Docker安装sentinel

```bash
#!/bin/bash


BASE_DIR="/usr/local/sentinel"
DASHBOARD_IP="192.168.31.105"
DASHBOARD_PORT="9412"
# 浏览器打开链接[https://github.com/alibaba/Sentinel/releases]下载个最新版, 提取版本号；
VERSION="1.8.3"
APP="sentinel-dashboard-${VERSION}"
DOCKER_FILE="${BASE_DIR}/Dockerfile"
mkdir -pv ${BASE_DIR}/logs

cd ${BASE_DIR} || exit

rm -rfv ${BASE_DIR}/${APP}.jar
wget https://github.com/alibaba/Sentinel/releases/download/${VERSION}/sentinel-dashboard-${VERSION}.jar

ll

rm -rfv ${DOCKER_FILE}
{
  echo FROM openjdk:11
  echo RUN mkdir -pv ${BASE_DIR}/
  echo ADD ${APP}.jar ${BASE_DIR}/${APP}.jar
  echo EXPOSE 8719 ${DASHBOARD_PORT}
  echo WORKDIR ${BASE_DIR}/
  echo ENTRYPOINT [\"java\",\"-jar\", \"${BASE_DIR}/${APP}.jar\"]
} >>${DOCKER_FILE} &&
  clear && cat ${DOCKER_FILE}

docker stop sentinel && docker rm -f sentinel
docker rmi -f sentinel:latest
docker build -f ${DOCKER_FILE} -t sentinel:latest .

docker stop sentinel && docker rm -f sentinel
docker run --name sentinel --restart=always \
  -p 9412:9412 \
  -p 8719:8719 \
  -e JAVA_OPTS="-Xms512m -Xmx512m" \
  -e SERVER_PORT="${DASHBOARD_PORT}" \
  -e CSP_SENTINEL_DASHBOAR_SERVER="${DASHBOARD_IP}:${DASHBOARD_PORT}" \
  -e PROJECT_NAME="sentinel-dashboard" \
  -e CSP_SENTINEL_LOG_DIR="${BASE_DIR}/logs" \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -v ${BASE_DIR}/logs:${BASE_DIR}/logs \
  -d sentinel:latest

clear && docker logs -f sentinel

rm -rfv ${APP}.jar

#访问sentinel控制面板: http://192.168.31.105:9412
```

## **2.21 Docker安装seata**

```bash
#!/bin/bash

#打开链接[https://github.com/seata/seata/releases]查看现在的版本号
CURRENT_VERSION="1.4.2"
OLD_VERSION="1.4.2"
DOWNLOAD_LINK="https://github.com/seata/seata/releases/download/v${CURRENT_VERSION}/seata-server-${CURRENT_VERSION}.tar.gz"

BASE_DIR="/usr/local/seata"
CONTAINER_NAME="seata-server"
IMAGE_NAME="seataio/seata-server"
WORK_DIR="${BASE_DIR}/${CONTAINER_NAME}"

mkdir -pv "${BASE_DIR}/"
mkdir -pv "${WORK_DIR}/"

cd ${BASE_DIR}/ || exit
rm -rfv seata-server-${CURRENT_VERSION}.tar.gz
wget ${DOWNLOAD_LINK}
rm -rfv "${BASE_DIR}/seata"
tar -C "${BASE_DIR}/" -xzvf "${BASE_DIR}/seata-server-${CURRENT_VERSION}.tar.gz"
mv -fv "${BASE_DIR}/seata/seata-server-${CURRENT_VERSION}" "${BASE_DIR}/seata-server-${CURRENT_VERSION}"
rm -rfv "${BASE_DIR}/seata"
ll ${BASE_DIR}/

#拉镜像
docker stop ${CONTAINER_NAME} && docker rm -f ${CONTAINER_NAME}
docker rmi -f ${IMAGE_NAME}:${OLD_VERSION}
docker pull ${IMAGE_NAME}:${CURRENT_VERSION}
clear && printf '\r\t\t\t\t%s\n\r\r' "Docker镜像列表" && docker images

docker network rm mynet
docker network create --driver bridge --subnet 172.18.0.0/16 --gateway 172.18.0.1 mynet

#复制文件
docker run --name ${CONTAINER_NAME} -p 8091:8091 -d ${IMAGE_NAME}:${CURRENT_VERSION}
docker cp ${CONTAINER_NAME}:/seata-server ${BASE_DIR}/
ll ${WORK_DIR}

docker stop ${CONTAINER_NAME} && docker rm -f ${CONTAINER_NAME}

#创建数据库[seata], 初始化sql，链接: https://github.com/seata/seata/tree/develop/script/server/db
#这里自行创建即可，不做演示。

#登录nacos，创建seata的nacos命名空间，改配置文件会用到，如：
#命名空间名称:命名空间ID, seata-server:f1c51b7c-562f-48c8-9b15-3a0ebd25dc2d, Group: SEATA_GROUP

#修改file.conf文件, 将mode修改为db, 然后把数据库配置[database store property]换成上面自己新建的seata数据库的配置
cd ${WORK_DIR}/resources/ || exit
cat file.conf
#这一步你可以使用vim修改file.conf，完成后保存并退出vim
vim file.conf

#修改registry.conf文件，将type修改为nacos, 将注册中心和配置中心改为自己上面创建的命名空间和对应的nacos IP地址
#主要修改两个代码块：registry {}, config {}; [dataId = "seataServer.properties"]可先加#号注释掉
vim registry.conf

#下载源码
cd ${BASE_DIR} || exit
wet https://github.com/seata/seata/archive/refs/tags/v${CURRENT_VERSION}.tar.gz
tar -xzvf "seata-${CURRENT_VERSION}.tar.gz"
cd ${BASE_DIR}/seata-${CURRENT_VERSION}/script/config-center || exit

# vim修改config.txt，将[store.mode=file]改为[store.mode=db],将数据库[store.db.*]改为自己数据库的配置
# [service.vgroupMapping.my_test_tx_group=default]改为[service.vgroupMapping.lejing_tx_group=default],
# 这个[lejing_tx_group]代码中会用到, 对应自己的事务的名, 根据自己的情况修改
vim config.txt

CONFIG_TEXT="${BASE_DIR}/seata-${CURRENT_VERSION}/script/config-center/config.txt"
# cat检查[config.txt]参数对不对
cat ${CONFIG_TEXT}

#接下来导入[config.txt]元数据到[nacos]中
NACOS_CONFIG="${BASE_DIR}/seata-${CURRENT_VERSION}/script/config-center/nacos/nacos-config.sh"
NACOS_HOST="192.168.31.23"
GROUP="SEATA_GROUP"
NAME_SPACE="f1c51b7c-562f-48c8-9b15-3a0ebd25dc2d"
chmod 777 -v ${NACOS_CONFIG}
cd "${BASE_DIR}/seata-${CURRENT_VERSION}/script/config-center/nacos/" || exit
#USAGE OPTION: $0 [-h host] [-p port] [-g group] [-t tenant] [-u username] [-w password]
${NACOS_CONFIG} -h ${NACOS_HOST} -p 8848 -g ${GROUP} -t ${NAME_SPACE}

#重启容器, 更多支持的环境变量参考: https://registry.hub.docker.com/r/seataio/seata-server
docker stop ${CONTAINER_NAME} && docker rm -f ${CONTAINER_NAME}
docker run --name ${CONTAINER_NAME} --restart=always \
  --net mynet \
  -p 8091:8091 \
  -v ${WORK_DIR}:/seata-server \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -e SEATA_IP="192.168.31.23" \
  -e SEATA_PORT="8091" \
  -d ${IMAGE_NAME}:${CURRENT_VERSION}

clear && docker logs -f ${CONTAINER_NAME}

rm -rfv ${BASE_DIR}/seata-${CURRENT_VERSION}.tar.gz
rm -rfv ${BASE_DIR}/seata-server-${CURRENT_VERSION}.tar.gz
rm -rfv ${BASE_DIR}/seata-server-${CURRENT_VERSION}
```

## 2.22 Docker安转oracle数据库

```bash
#!/bin/bash

IMG='registry.cn-hangzhou.aliyuncs.com/zhuyijun/oracle:19c'
WORK_DIR='/usr/local/oracle/oradata'
CONTAINER='oracle19c'

# pull image
# pull ${IMG}

# mkdir
rm -rfv "$WORK_DIR"
mkdir -p ${WORK_DIR}
chmod 777 ${WORK_DIR}

# account parameter
ORACLE_SID="lwj"
ORACLE_PDB="lwjpdb"
ORACLE_PWD="123456"

#nwtwork
docker network rm mynet
docker network create --driver bridge --subnet 172.18.0.0/16 --gateway 172.18.0.1 mynet

# run container
docker stop ${CONTAINER}
docker rm -f ${CONTAINER}
docker run --name ${CONTAINER} \
  --net mynet \
  -p 1521:1521 -p 5500:5500 \
  -e ORACLE_SID=${ORACLE_SID} \
  -e ORACLE_PDB=${ORACLE_PDB} \
  -e ORACLE_PWD=${ORACLE_PWD} \
  -e ORACLE_EDITION=standard \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -v ${WORK_DIR}:/opt/oracle/oradata \
  -d ${IMG}

#logs
docker logs -ft ${CONTAINER}

docker exec -it ${CONTAINER} /bin/bash

sqlplus / as sysdba

show pdbs;

# Setting SYS SYSTEM password
docker exec ${CONTAINER} ./setPassword.sh $ORACLE_PWD

docker exec -it --user root ${CONTAINER} /bin/bash

cp -rv /etc/skel/.bash_profile /root/

cp -rv /etc/skel/.bashrc /root/

yum install vi

vi ~/.bash_profile

source ~/.bash_profile

docker exec -it ${CONTAINER} /bin/bash

sqlplus system/123456@lwj

# select name,open_mode from v$pdbs;


# alter session set container=lwjpdb;
```

- 创建用户

```sql
CREATE USER "C##LWJ" IDENTIFIED BY "123456" DEFAULT TABLESPACE "SYSTEM";

GRANT "DBA", "PDB_DBA" TO "C##LWJ" WITH ADMIN OPTION;

ALTER USER "C##LWJ" DEFAULT ROLE "DBA", "PDB_DBA"
```

- 登录用户就可以在这个用户的名下建表

![image-20220722214009822](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20220722214009822.png)

![image-20220722214028242](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20220722214028242.png)

![image-20220722214210148](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20220722214210148.png)

## 2.23 docker安装mariadb

```bash
#!/bin/bash

WORK_DIR='/usr/local/mariadb'
IMG='mariadb:latest'
CONTAINER='mariadb'

mkdir -pv $WORK_DIR/{conf,conf/conf.d}
touch $WORK_DIR/conf/my.cnf $WORK_DIR/conf/conf.d/config-file.cn
chmod 777 -vR $WORK_DIR

docker rmi -f $IMG && docker pull $IMG

docker network rm mynet
docker network create --driver bridge --subnet 172.18.0.0/16 --gateway 172.18.0.1 mynet

docker stop $CONTAINER && docker rm -f $CONTAINER
docker run --name $CONTAINER \
  -p 3308:3306 \
  --net mynet \
  -v $WORK_DIR:/var/lib/mysql \
  -e MARIADB_ROOT_PASSWORD=123456 \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -d $IMG

clear && docker logs -f mariadb
```

- ## reset root and user passwords

  Adjust `myuser`, `databasename` and passwords as needed.

  ```shell
  # enter container
  docker exec -it mariadb bash
  
  mysql -u root -p123456
  
  use mysql;
  ```

  ```sql
  CREATE USER IF NOT EXISTS root@localhost IDENTIFIED BY '123456';
SET PASSWORD FOR root@localhost = PASSWORD ('123456');
GRANT ALL ON *.* TO root@localhost WITH GRANT OPTION;
CREATE USER IF NOT EXISTS root@'%' IDENTIFIED BY '123456';
SET PASSWORD FOR root@'%' = PASSWORD ('123456');
GRANT ALL ON *.* TO root@'%' WITH GRANT OPTION;

CREATE USER IF NOT EXISTS lwj@'%' IDENTIFIED BY '123456';
SET PASSWORD FOR lwj@'%' = PASSWORD ('123456');
CREATE DATABASE IF NOT EXISTS db_demo;
GRANT ALL ON db_demo.* TO lwj@'%';
  ```

```bash
docker restart mariadb
  ```

## 2.24 Docker 运行 SQL Server

[微软官方文档](https://docs.microsoft.com/zh-cn/sql/linux/quickstart-install-connect-docker?view=sql-server-ver16&pivots=cs1-bash)

```bash
#!/bin/bash

IMG='mcr.microsoft.com/mssql/server:latest'
WORK_DIR='/usr/local/sqlserver'
CONTAINER='sqlserver'

rm -rfv "$WORK_DIR"
mkdir -pv $WORK_DIR/{mssql,}
chmod 777 -vR $WORK_DIR

docker rmi -f $IMG && docker pull $IMG

docker network rm mynet
docker network create --driver bridge --subnet 172.18.0.0/16 --gateway 172.18.0.1 mynet

docker stop $CONTAINER && docker rm -f $CONTAINER
docker run --name $CONTAINER --hostname $CONTAINER \
  -p 1433:1433 \
  --net mynet \
  -e "ACCEPT_EULA=Y" \
  -e "SA_PASSWORD=weasley@dtt123" \
  -v $WORK_DIR/mssql:/var/opt/mssql \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -d $IMG

docker logs -f sqlserver

# 在容器内部使用完整路径通过 sqlcmd 进行本地连接, 没报错就成功了
docker exec -it sqlserver /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "weasley@dtt123"

```

## 2.25 Docker 运行 DB2

```bash
#!/bin/bash

IMG='ibmcom/db2:latest'
WORK_DIR='/usr/local/db2'
CONTAINER='db2'

rm -rfv "$WORK_DIR"
mkdir -pv $WORK_DIR/{database,}
chmod 777 -vR $WORK_DIR

docker rmi -f $IMG && docker pull $IMG

docker network rm mynet
docker network create --driver bridge --subnet 172.18.0.0/16 --gateway 172.18.0.1 mynet

docker stop $CONTAINER && docker rm -f $CONTAINER
docker run --name $CONTAINER --privileged=true \
  --net mynet \
  -p 50000:50000 \
  -e LICENSE=accept \
  -e DBNAME=testdb \
  -e DB2INSTANCE=weasley \
  -e DB2INST1_PASSWORD=123456 \
  -v $WORK_DIR/database:/database \
  -v /etc/timezone:/etc/timezone \
  -v /etc/localtime:/etc/localtime \
  -d $IMG

docker logs -f db2

docker exec -it db2 /bin/bash
```

## 2.16 docker安装MongoDB

- 安装

```bash
#!/bin/bash
docker stop mongo && docker pull mongo

#rm -rfv /usr/local/mongo/db
mkdir -pv /usr/local/mongo/db

docker stop mongo && docker rm mongo
docker run --name mongo --restart=always \
  -p 27017:27017 \
  --net mynet \
  -v /usr/local/mongo/db:/data/db \
  -e MONGO_INITDB_DATABASE=yapi \
  -e MONGO_INITDB_ROOT_USERNAME=yapipro \
  -e MONGO_INITDB_ROOT_PASSWORD=yapipro1024 \
  -d mongo
```

```shell
docker logs -f mongo
```

- 进入 MongoDB 容器后，进入 mongo cli

```shell
docker exec -it mongo /bin/bash
# 进入 MongoDB 容器后，进入 mongo cli
mongo localhost:27017
# 进入 MongoDB 的 mongo cli 后，执行以下语句进行初始化库表
```

![image-20221021030859760](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20221021030859760.png)

```mongodb
use admin;
db.auth("yapipro", "yapipro1024");
# 创建yapi数据库
use yapi;
# 创建给yapi使用的账号和密码，限制权限yapi是我们初始化的数据库
db.createUser({
  user: 'yapi',
  pwd: 'yapi123456',
  roles: [
 { role: "dbAdmin", db: "yapi" },
 { role: "readWrite", db: "yapi" }
  ]
});
```

![image-20221021031132532](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20221021031132532.png)

```bash
#退出Mongo Cli
exit;
#退出容器
exit
```

## 2.17 docker安装Yapi

> 到这一步你已经安装好`mongdb`了

[yapipro/yapi - Docker Image | Docker Hub](https://hub.docker.com/r/yapipro/yapi)

- 安装

```bash
docker rmi -f yapi && docker pull yapipro/yapi

docker stop yapi && docker rm yapi

BASE_DIR="/usr/local/yapi/conf"
mkdir -pv $BASE_DIR

#创建yapi配置文件,mail里面的换成自己的真实邮箱
tee $BASE_DIR/config.json <<-'EOF'
{
   "port": "3000",
   "adminAccount": "1432689025@qq.com",
   "timeout":120000,
   "db": {
     "servername": "mongo",
     "DATABASE": "yapi",
     "port": 27017,
     "user": "yapi",
     "pass": "yapi123456",
     "authSource": ""
   },
   "mail": {
     "enable": true,
     "host": "smtp.qq.com",
     "port": 465,
     "from": "*",
     "auth": {
       "user": "your_qq_email@qq.com",
       "pass": "your_password"
     }
   }
 }
EOF

# 创建容器
# 初始化管理员账号在上面的config.json配置中的: 1432689025@qq.com,  初始密码是: yapi.pro, 可以登录后进入个人中心修改
# 初始化数据库表
docker run -d --rm \
  --name yapi-init \
  --link mongodb:mongo \
  --net mynet \
  -v $BASE_DIR/config.json:/yapi/config.json \
   yapipro/yapi \
  server/install.js
 
docker stop yapi && docker rm yapi
docker run --name yapi --restart=always \
  --name yapi \
  --net mynet \
  -p 3000:3000 \
  -v $BASE_DIR/config.json:/yapi/config.json \
  -d yapipro/yapi \
  server/app.js
```

```bash
docker logs -f yapi
```

```bash
# 在服务器上验证yapi启动是否成功
curl http://127.0.0.1:3000/
```

- 初始化账号密码

user: 1432689025@qq.com

pwd: yapi.pro

## 2.18 docker安装xxl-job-admin

- <u>xxl-job</u>建表sql: [tables_xxl_job.sql](https://github.com/xuxueli/xxl-job/blob/master/doc/db/tables_xxl_job.sql)

- 查看docker镜像版本: [xuxueli/xxl-job-admin Tags | Docker Hub](https://hub.docker.com/r/xuxueli/xxl-job-admin/tags)

```bash
#!/bin/bash
# 配置文件示例: https://github.com/xuxueli/xxl-job/blob/master/xxl-job-admin/src/main/resources/application.properties
# docker版本标签: https://hub.docker.com/r/xuxueli/xxl-job-admin/tags

WORK_DIR="/usr/local/xxl-job"
CONTAINER_NAME="xxl-job-admin"
PREV_VERSION="2.3.0"
LATEST_VERSION="2.4.0"
JAVA_OPTS="-Xms512m -Xmx512m"
MYSQL_IP="mysql" # mysql 的容器名, 不是 docker 安装的 mysql 就具体 ip
SPRING_ARGS="--server.servlet.context-path=/job \
--spring.datasource.url=jdbc:mysql://$MYSQL_IP:3306/xxl_job?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai \
--spring.datasource.username=root \
--spring.datasource.password=123456
"

docker network create --driver bridge --subnet 172.18.0.0/16 --gateway 172.18.0.1 mynet

docker stop $CONTAINER_NAME && docker rm -f $CONTAINER_NAME

docker rmi xuxueli/xxl-job-admin:$PREV_VERSION
docker pull xuxueli/xxl-job-admin:$LATEST_VERSION

# centos
docker stop $CONTAINER_NAME && docker rm -f $CONTAINER_NAME
docker run --name $CONTAINER_NAME --restart=always \
  -p 8081:8080 \
  --net mynet \
  -e TZ=Asia/Shanghai \
  -e PARAMS="$SPRING_ARGS" \
  -e JAVA_OPTS="$JAVA_OPTS" \
  -v $WORK_DIR:/data/applogs \
  -d xuxueli/xxl-job-admin:$LATEST_VERSION

# ubuntu
docker stop $CONTAINER_NAME && docker rm -f $CONTAINER_NAME
docker run --name $CONTAINER_NAME --restart=always \
  -p 8081:8080 \
  --net mynet \
  -e PARAMS="$SPRING_ARGS" \
  -e JAVA_OPTS="$JAVA_OPTS" \
  -v $WORK_DIR:/data/applogs \
  -v /etc/timezone:/etc/timezone:ro -v /etc/localtime:/etc/localtime:ro \
  -d xuxueli/xxl-job-admin:$LATEST_VERSION
```

- 日志
```sh
docker logs -f xxl-job-admin
```


访问链接: http://宿主机ip:8081/job



## 2.19 安装携程Apollo注册中心

```sql
CREATE DATABASE IF NOT EXISTS `apollo_conf` CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_general_ci';
CREATE DATABASE IF NOT EXISTS `apollo_portal` CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_general_ci';
```

[配置中心数据库脚本](https://github.com/apolloconfig/apollo-quick-start/blob/master/sql/apolloconfigdb.sql)

- 安装脚本（随宿主机启动）

```bash
#!/bin/bash

WORK_DIR="/usr/local/apollo"
mkdir -pv $WORK_DIR $WORK_DIR/logs
cd ${WORK_DIR}

#数据库配置
DB_HOST="192.168.31.139"
DB_PORT="3306"
DB_NAME="ApolloConfigDB"
DB_USERNAME="root"
DB_PASSWORD="123456"
JAVA_OPTS="-Xms256m -Xmx256m -Xmn256m"
PRO_META_HOST="192.168.31.139"
CONFIG_SERVICE_PORT="8081"
ENV="pro"

# Apollo Config Service
CONTAINER_NAME="apollo-configservice"
docker stop $CONTAINER_NAME && docker rm -f $CONTAINER_NAME
docker run --name $CONTAINER_NAME --restart=always \
  -p $CONFIG_SERVICE_PORT:8080 \
  --net mynet \
  -e TZ=Asia/Shanghai \
  -e JAVA_OPTS="$JAVA_OPTS" \
  -e SPRING_DATASOURCE_URL="jdbc:mysql://$DB_HOST:$DB_PORT/$DB_NAME?characterEncoding=utf8" \
  -e SPRING_DATASOURCE_USERNAME=$DB_USERNAME \
  -e SPRING_DATASOURCE_PASSWORD=$DB_PASSWORD \
  -e PRO_META=http://$PRO_META_HOST:$CONFIG_SERVICE_PORT \
  -v $WORK_DIR/logs:/opt/logs \
  -d apolloconfig/${CONTAINER_NAME}:latest

docker logs -f apollo-configservice

# Apollo Admin Service
CONTAINER_NAME="apollo-adminservice"
docker stop $CONTAINER_NAME && docker rm -f $CONTAINER_NAME
docker run --name $CONTAINER_NAME --restart=always \
  -p 8090:8090 \
  --net mynet \
  -e TZ=Asia/Shanghai \
  -e JAVA_OPTS="$JAVA_OPTS" \
  -e SPRING_DATASOURCE_URL="jdbc:mysql://$DB_HOST:$DB_PORT/$DB_NAME?characterEncoding=utf8" \
  -e SPRING_DATASOURCE_USERNAME=$DB_USERNAME \
  -e SPRING_DATASOURCE_PASSWORD=$DB_PASSWORD \
  -e APOLLO_PORTAL_ENVS="${ENV}" \
  -e PRO_META=http://$PRO_META_HOST:$CONFIG_SERVICE_PORT \
  -v $WORK_DIR/logs:/opt/logs \
  -d apolloconfig/$CONTAINER_NAME:latest

docker logs -f apollo-adminservice

# Apollo Portal
DB_NAME="ApolloPortalDB"
CONTAINER_NAME="apollo-portal"
docker stop $CONTAINER_NAME && docker rm -f $CONTAINER_NAME
docker run --name $CONTAINER_NAME --restart=always \
  -p 8070:8070 \
  --net mynet \
  -e TZ=Asia/Shanghai \
  -e JAVA_OPTS="$JAVA_OPTS" \
  -e SPRING_DATASOURCE_URL="jdbc:mysql://$DB_HOST:$DB_PORT/$DB_NAME?characterEncoding=utf8" \
  -e SPRING_DATASOURCE_USERNAME=$DB_USERNAME \
  -e SPRING_DATASOURCE_PASSWORD=$DB_PASSWORD \
  -e APOLLO_PORTAL_ENVS="${ENV}" \
  -e PRO_META=http://$PRO_META_HOST:$CONFIG_SERVICE_PORT \
  -v $WORK_DIR/logs:/opt/logs \
  -d apolloconfig/$CONTAINER_NAME:latest

docker logs -f apollo-portal

```

Apollo Config Service

```bash
docker stop apollo-configservice && docker rm -f apollo-configservice
docker run -p 8081:8080 \
  -e SPRING_DATASOURCE_URL="jdbc:mysql://192.168.31.139:3306/ApolloConfigDB?characterEncoding=utf8" \
  -e SPRING_DATASOURCE_USERNAME=root \
  -e SPRING_DATASOURCE_PASSWORD=123456 \
  -e JAVA_OPTS="-Deureka.instance.homePageUrl=http://192.168.31.139:8081" \
  -d -v /tmp/logs:/opt/logs \
  --name apollo-configservice apolloconfig/apollo-configservice
```

Apollo Admin Service

```bash
docker stop apollo-adminservice && docker rm -f apollo-adminservice
docker run -p 8090:8090 \
  -e SPRING_DATASOURCE_URL="jdbc:mysql://192.168.31.139:3306/ApolloConfigDB?characterEncoding=utf8" \
  -e SPRING_DATASOURCE_USERNAME=root \
  -e SPRING_DATASOURCE_PASSWORD=123456 \
  -e APOLLO_PORTAL_ENVS=pro \
  -e PRO_META=http://192.168.31.139:8081 \
  -d -v /tmp/logs:/opt/logs \
  --name apollo-adminservice apolloconfig/apollo-adminservice
```

Apollo Portal

```bash
docker stop apollo-portal && docker rm -f apollo-portal
docker run -p 8070:8070 \
  -e SPRING_DATASOURCE_URL="jdbc:mysql://192.168.31.139:3306/ApolloPortalDB?characterEncoding=utf8" \
  -e SPRING_DATASOURCE_USERNAME=root \
  -e SPRING_DATASOURCE_PASSWORD=123456 \
  -e APOLLO_PORTAL_ENVS=pro \
  -e PRO_META=http://192.168.31.139:8081 \
  -d -v /tmp/logs:/opt/logs \
  --name apollo-portal apolloconfig/apollo-portal
```

# 3 Docker配置和操作

## 3.1 docker防火墙的配置

```shell
#关闭防火强
systemctl disable firewalld

#查看防火状态
systemctl status firewalld

#永久关闭防火墙
systemctl stop firewalld && systemctl disable firewalld

#查看防火状态
systemctl status firewalld

#重新加载systemctl配置以及重启docker服务
systemctl daemon-reload && systemctl restart docker.service
```

## 3.2 docker远程连接配置

```shell
#1. 修改docker配置文件
vim /lib/systemd/system/docker.service

#2. 修改ExecStart这一行，把原来的前面加上#号注释掉，结尾加上"  -H tcp://0.0.0.0:2375",添加后如下:
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock -H tcp://0.0.0.0:2375


#3. 退出vim编辑器，重新加载配置以及重启服务
systemctl daemon-reload
systemctl restart docker.service
#4.开启2375端口的访问权限；如果是“xxx云”服务器，记得开启2375端口，同时使用https的加密协议连接docker，防止攻击！！！
firewall-cmd --zone=public --add-port=2375/tcp --permanent
firewall-cmd --reload
firewall-cmd --list-ports

#使用curl验证下
curl http://127.0.0.1:2375/version

```

- 验证是否成功开启远程,替换成你自己的ip
  http://192.168.40.132:2375/version

![image-20201105000834216](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20201105000834216.png)

## 3.3 开启docker容器随docker自启动

> 语法：

```shell
docker update 容器名称/容器id --restart=always
```

> 示例：

```shell
docker update elasticsearch --restart=always
docker update kibana --restart=always
docker update portainer --restart=always
docker update redis --restart=always
docker update mysql --restart=always
clear && docker ps -a
```

## 3.4 docker容器批量操作

- 关闭docker中所有的容器

```shell
docker stop $(docker ps -a | awk '{ print $1}' | tail -n +2)
```

- 启动docker中所有的容器

```shell
docker start $(docker ps -a | awk '{ print $1}' | tail -n +2)
```

- 删除docker中所有的容器

```shell
docker rm -f $(docker ps -a | awk '{ print $1}' | tail -n +2)
```

- 删除docker中所有的镜像

```shell
docker rmi $(docker images | awk '{print $3}' |tail -n +2)
```

## 3.5 创建自定义网络环境解决同一宿主机上不同docker容器网络互通问题

>  建议使用自定义的网桥来控制哪些容器可以相互通信，还可以自动 DNS 解析容器名称到IP地址。Docker
> 提供了创建这些网络的默认网络驱动程序，你可以创建一个新的 Bridge 网络，Overlay 或 Macvlan 网络。你还可以创建一个网络插件或远程网络进行完整的自定义和控制。
>
>  你可以根据需要创建任意数量的网络，并且可以在任何给定时间将容器连接到这些网络中的零个或多个网络。此外，您可以连接并断开网络中的运行容器，而无需重新启动容器。当容器连接到多个网络时，其外部连接通过第一个非内部网络以词法顺序提供。
>
>  一个 Bridge 网络是 Docker 中最常用的网络类型。桥接网络类似于默认 Bridge
> 网络，但添加一些新功能并删除一些旧的能力。以下示例创建一些桥接网络，并对这些网络上的容器执行一些实验。

```shell
docker network rm mynet
docker network create --driver bridge --subnet 172.18.0.0/16 --gateway 172.18.0.1 mynet
```

- 查看docker网络列表

  ```shell
  docker network ls
  ```

![image-20210303011433042](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210303011433042.png)

- 查看我们创建的网络环境

```shell
docker network inspect mynet
```

![image-20210303011337417](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210303011337417.png)

- 创建两个容器是可以 `ping` 通的

```shell
docker rm -f web_app_1 && docker run -d -p 8001:7070 --name web_app_1 --net mynet springboot-jenkins-demo:1.0.0-SNAPSHOT
docker rm -f web_app_2 && docker run -d -p 8002:7070 --name web_app_2 --net mynet springboot-jenkins-demo:1.0.0-SNAPSHOT
```

mynet下面有两个容器：`docker network inspect mynet`

![image-20210303014937554](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210303014937554.png)

```bash
docker exec -it web_app_1 bash
apt-get -y update && apt-get  -y install iputils-ping

docker exec -it web_app_2 bash
apt-get -y update && apt-get  -y install iputils-ping


docker exec -it web_app_1 ping web_app_2
docker exec -it web_app_2 ping web_app_1

```

内网互通：

![image-20210303024800759](https://alphahub-test-bucket.oss-cn-shanghai.aliyuncs.com/image/image-20210303024800759.png)

- 网络连通

> 基于 **docker0** 的容器和基于 **mynet** 的容器之间是不互通的，所以需要进行网络连通，还是以 **springboot-jenkins-demo.jar
** 为例：

```shell
docker rm -f web_app_3 && docker run -d -p 8003:7070 --name web_app_3 springboot-jenkins-demo:1.0.0-SNAPSHOT

docker exec -it web_app_3 bash
apt-get -y update && apt-get  -y install iputils-ping
```

1. 现在将 **web_app_3**连接到 **mynet**

```shell
docker network connect mynet web_app_3
```

2. 现在查看**mynet**的网络状况

```shell
docker network inspect mynet
```

**Tips**： 一个容器，2 个 ip，类似于云服务器，有公网ip 和内网 ip

3. 测试网络是否可以互通，我们使用**web_app_3**去**ping web_app_1**可以**ping**通，但是 **web_app_2**还是 **ping**
   不通的：话不多数，上代码：

```shell
# web_app_3 连接 web_app_1
docker exec -it web_app_3 ping web_app_1

# web_app_3 连接 web_app_2
docker exec -it web_app_3 ping web_app_2
```

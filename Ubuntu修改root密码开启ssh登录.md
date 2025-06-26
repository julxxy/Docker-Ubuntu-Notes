# 1 Ubuntu安装SSH

## 1.1 安装openssh-server

>  SSH分客户端openssh-client和openssh-server
>  默认情况下已经安装了客户端openssh-client
>  若希望其他可以远程登陆，要打开openssh-server

```shell
# 查看安装情况
dpkg -l | grep ssh
sudo apt-get install -y openssh-server

# 查看已安装的软件
dpkg -l

# 查找某个名字为zero，| grep 是管道
dpkg -l | grep zero

#查看进程命令
ps -e

# 启动ssh
sudo /etc/init.d/ssh start
sudo service ssh start

# 修改ssh-server配置文件:/etc/ssh/sshd_config, 开启允许root用户登录
# 在这里可以定义SSH的服务端口，默认端口是22，你可以自己定义成其他端口号，如222
vim /etc/ssh/sshd_config
#
# 将 "PermitRootLogin without-password" 改为 "PermitRootLogin yes"
#
# 退出vim编辑器
:wq

# 重启ubuntu系统
reboot now

```



## 1.2 修改root密码



```shell
#以普通用户登录系统，会让你输入两次密码
sudo passwd root

#然后重启系统即可使用root用户登录系统了
reboot

```



## 1.3 安装网络插件

> ubuntu容器安装ping ifconfig ip命令

```shell
### ifconfig
apt-get -y install net-tools

###  ping
apt-get -y install iputils-ping

####  ip
apt-get -y install iproute2

### zip
apt-get -y install zip
apt-get -y install unzip
```



## 1.4 修改系统时区

```shell
#选择相应所谓时区即可
dpkg-reconfigure tzdata
```



## 1.5 修改A stop job is running for ...

```shell
vim /etc/systemd/system.conf

#修改下面两个变量为（默认等待时间为90秒）：
DefaultTimeoutStartSec=5s
DefaultTimeoutStopSec=5s

#执行
systemctl daemon-reload
```


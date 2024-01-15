基于docker创建`ubuntu:20.04`容器并实现ssh连接

> 这里强烈建议用更稳定一些的20.04版本

创建并运行容器，指定容器名为`ubuntu`

```bash
docker run -itd -p 20022:22 --name ubuntu ubuntu:22.04
```

- -p参数进行端口映射，也可以映射端口多个， `host-port:container-port`

创建后继续下面指令进入容器内部

```
docker attach ubuntu
```

创建密码，并更新apt源

```log
passwd
apt-get update

```

安装基础包

```shell
apt-get install vim
apt-get install -y ca-certificates
apt-get install ssh
```

开放root连接权限

```log
vim /etc/ssh/sshd_config
```

找到 `#PermitRootLogin prohibit-password`，修改为 PermitRootLogin yes

启动`ssh`,`service ssh start`

`ctrl + p   ctrl + q` 退出容器

尝试ssh连入容器

```
ssh root@xxx.xx.xxx.xx -p 20022
```



## 安装基本依赖

apt-get install -y gnupg2 gpgv2



Tingshua sources:

```
deb https://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse

```

switch.sh

```shell
# /bin/bash

mv sources.list 	sources.list.back2
mv sources.list.back 	sources.list
mv sources.list.back2 	sources.list.back
apt update
```



```
sudo apt-get install  gcc automake autoconf libtool make git
```






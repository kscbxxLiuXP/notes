## go安装

先在这个链接(<https://go.dev/doc/install>)下载最新的压缩包

    sudo su
    rm -rf /usr/local/go && tar -C /usr/local -xzf go1.21.0.linux-amd64.tar.gz

编辑`/etc/profile`

    gedit /etc/profile

在文件的最下面添加

    export PATH=$PATH:/usr/local/go/bin

立即生效

    source /etc/profile

同理，编辑`bashrc`

    gedit ~/.bashrc

在`bashrc`的最下面添加

    source /etc/profile

配置代理
在`shell`中执行下面的代码

```bash
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
```
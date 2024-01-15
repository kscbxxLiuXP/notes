# NodeJS

## 下载binary包

从官网找到最新版本的`nodejs`，

> https://nodejs.org/en/download/

```bash
wget https://nodejs.org/dist/v20.10.0/node-v20.10.0-linux-x64.tar.xz
```

## 安装

解压安装包

```bash
 sudo mkdir -p /usr/local/lib/nodejs
 sudo tar -xvf node-v20.10.0-linux-x64.tar.xz -C /usr/local/lib/nodejs 
```

配置环境变量`.profile`

```
export PATH=/usr/local/lib/nodejs/node-v20.10.0-linux-x64/bin:$PATH
```

使环境变量生效

```
. ~/.profile
```

## 验证

`node -v`

`npm -v`

`npx -v`
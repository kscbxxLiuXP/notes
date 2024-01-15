---
title:1
asd
---

# Docker安装



## 安装docker

1. 打开终端并更新软件包列表：

   ```
   sudo apt-get update
   ```

2. 安装必要的依赖项：

   ```bash
   sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release
   ```

3. 添加Docker的官方GPG密钥：

   ```
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   ```

4. 添加Docker的官方APT仓库：

   ```
   echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

5. 更新软件包列表：

   ```
   sudo apt-get update
   ```

6. 安装Docker：

   ```
   sudo apt-get install docker-ce docker-ce-cli containerd.io
   ```

7. 安装完成后，您可以通过运行以下命令来检查Docker是否已成功安装：

   ```
   docker --version
   ```

8. 如果您想使用Docker而不需要sudo权限，请将当前用户添加到docker用户组中：

   ```
   sudo usermod -aG docker $USER
   ```

9. 重新登录以使更改生效。 

## 镜像源

```plain

sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://klt67w4c.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## Docker-compose

```bash
sudo apt install docker-compose
```

注意，此处的`docker-compose`需要依赖`python3`，安装完成后假如出现报错，可以尝试：

```bash
pip install 'urllib3<2'
```


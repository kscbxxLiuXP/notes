# mysql

## docker安装mysql

首先拉取镜像：

```
docker pull mysql
```

然后从镜像创建容器,这里我们使用`docker-compose`来启动，编写一个`yml`文件：

```
version: '3'
services:
  mysql:
    image: mysql
    restart: always
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      TZ: Asia/Shanghai
    ports:
      - 3306:3306
    volumes:
      - ./data:/var/lib/mysql
    command: --max_connections=1000 --character-set-server=utf8mb4 --collation-server=utf8mb4_general_ci --default-authentication-plugin=mysql_native_password

```


# NextCloud

## 介绍

本文将主要介绍如何使用`docker-compose`部署nextcloud

部署的

nginx proxy manager

## docker-compose

```yaml
version: '3'

volumes:
  db:
  nextcloud: 

services:
  db:
    image: mariadb:10.6
    container_name: nextcloud_db
    command: --transaction-isolation=READ-COMMITTED --log-bin=binlog --binlog-format=ROW --innodb_read_only_compressed=0
    restart: always
    volumes:
      - db:/var/lib/mysql:Z
    environment:
      - MYSQL_ROOT_PASSWORD=123456
      - MARIADB_AUTO_UPGRADE=1
      - MARIADB_DISABLE_UPGRADE_BACKUP=1
    env_file:
      - db.env

  redis:
    image: redis:alpine
    container_name: nextcloud_redis
    restart: always

  app:
    image: nextcloud:fpm-alpine
    container_name: nextcloud_app
    restart: always
    volumes:
      - ./nextcloud:/var/www/html:z
    environment:
      - MYSQL_HOST=db
      - REDIS_HOST=redis
    env_file:
      - db.env
    depends_on:
      - db
      - redis

  web:
    image: nginx:alpine
    container_name: nextcloud_web
    restart: always
    ports:
      - 3090:80
    volumes:
      - ./nextcloud:/var/www/html:z,ro
      - ./nginx/log:/var/log/nginx
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/cert:/etc/nginx/cert
    depends_on:
      - app

  cron:
    image: nextcloud:fpm-alpine
    container_name: nextcloud_cron
    restart: always
    volumes:
      - ./nextcloud:/var/www/html:z
    entrypoint: /cron.sh
    depends_on:
      - db
      - redis

  # onlyoffice:
  #   image: onlyoffice/documentserver
  #   container_name: onlyoffice
  #   restart: always
  #   ports:
  #     - 9000:443
  #   volumes:
  #     - ./ssl/onlyoffice.crt:/var/www/onlyoffice/Data/certs/onlyoffice.crt
  #     - ./ssl/onlyoffice.key:/var/www/onlyoffice/Data/certs/onlyoffice.key

```


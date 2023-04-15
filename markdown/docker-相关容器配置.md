# Docker 相关容器配置

更新时间：2023年4月9日

自从服务器重置以后，重新安装了 Docker，下面记录一下正在使用的容器和相关配置内容。

## 创建容器间网络

```shell
docker network create -d bridge individual
```

## Nginx

```shell
docker run -d \
--name nginx \
-p 80:80 \
-p 443:443 \
-v $PWD/nginx/html:/usr/share/nginx/html \
-v $PWD/nginx/nginx.conf:/etc/nginx/nginx.conf \
-v $PWD/nginx/log:/var/log/nginx \
-v $PWD/nginx/cert:/etc/nginx/cert \
-v $PWD/nginx/conf.d/default.conf:/etc/nginx/conf.d/default.conf \
--restart=always \
nginx 
```

## MySQL

```shell
docker run -d \
--name mysql \
-p 3306:3306 \
-v $PWD/mysql/log:/var/log/mysql \
-v $PWD/mysql/data:/var/lib/mysql \
-v $PWD/mysql/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
--restart=always \
--network individual mysql --lower_case_table_names=1
```

### MySQL 密码修改

```shell
ALTER USER 'root'@'%' IDENTIFIED WITH [mysql_native_password,caching_sha2_password] BY '123456';
```

### MySQL 配置

进入 `$PWD/mysql/conf/conf.d/` 创建 `mysqld.cnf` 文件，粘贴以下内容：

```conf
[mysqld]
performance_schema_max_table_instances=400
table_definition_cache=400 #缓存
performance_schema=off #用于监控MySQL server在一个较低级别的运行过程中的资源消耗、资源东西
table_open_cache=64 #打开表的缓存
innodb_buffer_pool_chunk_size=64M #InnoDB缓冲池大小调整操作的块大小
innodb_buffer_pool_size=64M #InnoDB 存储引擎的表数据和索引数据的最大内存缓冲区大小
```

## phpMyAdmin

```shell
docker run -d \
--name phpmyadmin \
--network individual \
-e PMA_HOST="mysql" \
-p 80:80 \
--restart=always \
phpmyadmin 
```

## Redis

创建目录：  
`mkdir -p $PWD/redis/conf`  
创建文件：  
`touch $PWD/redis/conf/redis.conf`

```shell
docker run -d \
--name redis \
-p 6379:6379 \
--network individual \
-v $PWD/redis/data:/data \
-v $PWD/redis/conf:/etc/redis \
--restart=always \
redis \
redis-server /etc/redis/redis.conf \
--appendonly yes
```

## Warchtowe

```shell
docker run -d \
--name watchtower \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock  \
containrrr/watchtower \
--cleanup \
-i 3600
```

## WordPress

```shell
docker run -d \
--name wordpress \
--network individual \
--restart=always \
-v $PWD/wordpress:/var/www/html \
-p 8080:80 \
wordpress
```

## Vaultwarden

```shell
docker run -d \
--name vaultwarden \
--restart=always \
-v $PWD/vaultwarden:/data \
-p 80:80 \
vaultwarden/server:latest
```

## QianDao

```shell
docker run -d \
--name qiandao \
--restart=always \
-p 80:80 \
-v $PWD/qiandao/config:/usr/src/app/config \
a76yyyy/qiandao
```

## QingLong

```shell
docker run -dit \  
--name qinglong \  
--restart=always \  
-p 5775:5700 \  
-v $PWD/ql:/ql/data \  
whyour/qinglong:latest
```
